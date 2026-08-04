# context-verdict 🔎 — the outside auditor

🌐 **English** · [Português (BR)](./README.pt-BR.md)

A single-specialist skill that judges **the current session's own context quality** — signal vs. noise — from the raw transcript on disk, immune to the session's own degradation.

## Why

A model assessing its own context window has a blind spot: as the window fills and quality degrades, its *self-assessment* degrades right along with it — a tired model also judges its own tiredness poorly. This skill sidesteps that by dispatching a fresh subagent (`context-auditor`) that never saw the conversation, reads the `.jsonl` transcript as bytes, computes deterministic metrics, and returns an evidence-based verdict.

It's the qualitative complement to a context-% meter: the meter tells you *how full*, the auditor tells you *how much of that is still worth keeping*.

> ⚠️ **Built for Claude Code specifically — more so than this house's other skills.** The auditor reads the session transcript as a `.jsonl` file at Claude Code's on-disk path convention, and the agent file uses Claude Code's subagent frontmatter (`name`/`tools`/`model`/`effort`). Porting to Codex, OpenCode, Grok, or another harness means rewriting *how the transcript is located and parsed* (Step 1 of `SKILL.md`), not just swapping a config value — check whether your harness even persists a raw, greppable transcript before adopting this one.

## The cast

| Role | Job |
|------|-----|
| 🔎 **context-auditor** | Reads the transcript JSONL, computes noise ratio / error density / thrash signals, samples the heaviest dumps, and rules 🟢/🟡/🔴 |
| 🎩 **the main thread** | Resolves the transcript path, states the current objective, dispatches the auditor, relays the verdict in its own voice |

## How it works

```
State the current objective (one line)
   → dispatch context-auditor with transcript path + objective
   → auditor computes metrics from disk (jq/wc/grep, never a full Read)
   → auditor samples the heaviest tool_result dumps, judges relevance
   → verdict: 🟢 LIMPO / 🟡 RUIDOSO / 🔴 CRÍTICO + justification
   → main thread relays it, flagging any disagreement with its own impression
```

## Verdict rubric

- 🟢 **clean** — stale/noise volume under ~40%, few or no errors, no thrash. Keep working.
- 🟡 **noisy** — ~40–70% stale, or several failed commands/spent explorations dominating the window. Worth weighing a cleanup now.
- 🔴 **critical** — over ~70% stale, or repeated error loops / heavy thrash. Clean before the next task, no debate.

Thresholds are guidance, not law — a noisy-looking window that's still fully relevant to the current objective can stay 🟢; a small transcript full of dead ends can be 🔴.

## Files

```
SKILL.md                              → the dispatch protocol (main thread)
agents/context-verdict/context-auditor.md  → the auditor (subagent)
SETUP.md                              → install (skill + agent) and prerequisites
```

## Install

Requires a harness where the main thread can spawn subagents and the subagent can read files from disk (e.g. Claude Code). Copy the skill into your skills directory and the agent into your agents directory — see [`SETUP.md`](./SETUP.md).

## Use

> *"context-verdict"* / *"qual a qualidade do contexto?"* / at a task boundary, before deciding whether to clear.

Trigger it at task boundaries — after finishing a task, near a context-% threshold you've set for yourself, or before a heavy next task — not mid-task, since the verdict is only accurate up to the moment you fire it.

## Customise

- **`agents/context-verdict/context-auditor.md`** — retune the rubric thresholds, the metrics computed, or the model tier (ships as Sonnet, low effort — cheap by design).
- **Language** — the verdict output defaults to pt-BR; translate the auditor's output block and the skill's relay wording for another language.
- **Transcript path resolution** — `SKILL.md` Step 1 assumes Claude Code's on-disk convention; adjust if your harness differs.

## License

MIT.
