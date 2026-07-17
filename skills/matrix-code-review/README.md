# matrix-code-review 🕶️

🌐 **English** · [Português (BR)](./README.pt-BR.md)

A **multi-model, multi-vendor code review** orchestrated by one agent, themed on *The Matrix*. One leader terminal (**Morpheus**) triages a git diff, recruits a crew of specialist reviewers — each a different model — runs them in parallel, and consolidates their findings into a single verdict, documenting where they disagreed.

Built to run on [Maestri](https://www.themaestri.app) (Maestro Mode recruits real terminals on a canvas), with a Claude-only fallback for anywhere else. **Language- and framework-agnostic** — it reviews any git diff (C#, TypeScript/React, Go, Python…), no stack assumptions.

## Why

A single model reviewing its own blind spots isn't a review — it's an echo. Different model *families* miss different things. This skill puts Claude, DeepSeek, and a large-context model on the same diff from different angles, then has a strong reasoner adjudicate the criticals. The heavy tier only fires for high-risk changes; everyday diffs get a lean two-reviewer pass.

## The crew

| Codename | Role | Model profile | Examples |
|----------|------|---------------|----------|
| 🕶️ **Morpheus** | Orchestrator — triage, recruit, consolidate | Your strongest model | Claude Opus, Claude Fable, GPT‑5 |
| ☎️ **Link** | Triage — fast, cheap first pass | A fast, cheap model | DeepSeek Flash, Claude Haiku, Gemini Flash |
| 🥋 **Neo** | Depth — the primary review | A disciplined reviewer | Claude Sonnet, GPT‑5, Gemini Pro |
| 🕴️ **Agent Smith** | Adversarial — the independent skeptic | A different vendor than Neo | DeepSeek V4 Pro, GPT‑5, Grok |
| 🏛️ **The Architect** | Large-context — cross-file contracts | A large-window model | Kimi K3, Gemini Pro (1M) |
| 🔮 **The Oracle** | Escalation — final authority on 🔴 | Your best reasoner | Claude Opus, GPT‑5 |

Codename = a stable role. The model behind it is swappable — the examples are just illustrative, use whatever you have.

## How it works

```
Morpheus (triage) → recruits the crew by risk → dispatches in parallel
   → each reviewer works independently → Morpheus consolidates + resolves divergences
   → one report: findings, sources, disputes, verdict
```

- **Triage** scores the diff (size, security surface, migrations, contracts…) → low / medium / high.
- **Dispatch** scales to risk: low = Link only; medium = Neo + Smith; high = all five.
- **Synthesis** cross-references findings, and the Oracle confirms or refutes the criticals.

Two execution modes:
- **Maestri mode** — Morpheus recruits real terminals (each a different CLI/model) via Maestro Mode, coordinating through shared notes. See [`SETUP-maestri.md`](./SETUP-maestri.md).
- **Fallback mode** — no Maestri? It spawns Claude sub-agents via the `Agent` tool. Multiple perspectives, single vendor.

## Files

```
SKILL.md            → the skill definition (contract, triage, crew, synthesis)
checks.md           → generic review checklist (SEC/COR/PERF/ARCH/API/QUAL/TEST)
SETUP-maestri.md    → one-time Maestri config walkthrough
roles/              → the brief for each reviewer
  link.md · neo.md · smith.md · architect.md · oracle.md
```

## Install

Drop the folder into your agent's skills directory (e.g. `~/.claude/skills/matrix-code-review`). On Maestri, keep it under a scanned Skills location so the orchestrator sees it.

## Quick start

**On Maestri:** do the one-time setup in [`SETUP-maestri.md`](./SETUP-maestri.md), then in a Maestro terminal say:
> *"matrix code review of the changes since main"* — or — *"passa o review para o Morpheus na branch feature/xyz"*

**Anywhere else (Claude):** invoke the skill and point it at a base ref:
> *"matrix code review, base = main"*

Optionally pass a spec/PRD/issue file so reviewers can verify acceptance criteria, and `--out <path>` to place the report. Default output is `./matrix-review-<date>.md`.

## Tuning the crew (models & cost)

The **codename → model mapping is a knob, not a rule.** Each role is model-agnostic — wire in whatever CLIs/models you run, keep the codenames, and **experiment to see what works best for your stack**. A few things worth knowing:

- **Vendor diversity is the payoff, but it's optional.** You can run the whole crew on one vendor — 100% Anthropic, 100% OpenAI, whatever — and it works fine. You just trade away the cross-vendor angle: models from *different* families miss different things, and a same-family crew shares blind spots. Mix vendors on the roles that matter most (Neo vs Smith is the key pair) if you want that coverage.
- **Big-reasoning / large-context models are powerful but token-hungry.** The Architect seat (e.g. **Kimi K3**) brings serious reasoning and a huge window — excellent at cross-file contract bugs — but a large diff fed to it can **burn through a token/usage quota fast and stall the run**. If that bites you:
  - reserve the large-context reviewer for genuinely high-risk / large diffs (the triage already gates it to high risk);
  - or give that seat a lighter model;
  - or trim what you feed it (only the touched files + immediate context, not the whole repo).
- **The triage tiers exist to control cost.** Low-risk changes run one cheap reviewer; the expensive five-model tier only fires on high risk. Adjust the thresholds in `SKILL.md` §3 to taste.

- **It's language-agnostic — enrich `checks.md` for depth.** The reviewers already read C#, TypeScript/React, Go, Python, etc.; point them at any repo and it works. The categories in `checks.md` are language-neutral, so the way to make a review *sharp* for a stack is to add that stack's best practices there (and/or in `roles/*.md`). You're not teaching the model the language — you're focusing its attention. Examples:
  - **React/TS**: hooks rules, effect dependency arrays, `key` on lists, `dangerouslySetInnerHTML`/XSS, memoization, accessibility, bundle size.
  - **C#/.NET**: `async`/`await` and `ConfigureAwait`, `IDisposable`/`using`, EF query patterns (N+1, tracking), nullable reference types.

Everything else is tunable too: the categories in **`checks.md`** and each reviewer's brief in **`roles/*.md`**.

## License

MIT.
