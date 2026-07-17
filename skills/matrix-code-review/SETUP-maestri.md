# Setup — running matrix-code-review on Maestri

[Maestri](https://www.themaestri.app) is a macOS canvas that orchestrates multiple agent terminals visually. This skill uses **Maestro Mode**: one leader terminal (Morpheus) recruits a crew of specialist reviewer terminals on demand, each running a different CLI/model. This guide is the one-time setup.

> No Maestri? The skill still works in **fallback mode** — it spawns Claude sub-agents via the `Agent` tool. Multiple perspectives, single vendor. Skip this file and just invoke the skill.

## How Maestri maps to the skill

Maestri has **two independent concepts** the skill combines:

- **Preset** (Settings → Terminal → "Presets de início rápido") = *which CLI to launch* — `Nome + Comando` (e.g. `claude`, `opencode`) + an optional **Maestro** flag. **A preset has no model field** — pin the model in the command args.
- **Responsabilidade** (Settings → Agentes → Responsabilidades) = *a named instruction set* assigned to a terminal. This is where each reviewer's role brief goes.

**Recruiting a reviewer = a preset (the CLI at the right model) + a Responsabilidade (the role brief).**

Maestri also scans skill locations (Settings → Agentes → Skills). If you keep this skill under one of them (e.g. `~/.claude/skills`), the Morpheus terminal sees it automatically.

---

## Step 1 — Install the CLIs you want

Each reviewer runs a code-agent CLI. Install whichever you'll use — e.g. **Claude Code** (`claude`) and **OpenCode** (`opencode`). Different vendors are the whole point: different model families have different blind spots.

## Step 2 — Create the presets (Settings → Terminal)

One preset per (CLI + model). The model goes in the **Comando** args. Up to 5 presets show as quick-launch in the terminal modal (a display limit, not a hard cap).

| Preset name | Comando (example) | Maestro | Reviewer |
|-------------|-------------------|:-:|----------|
| `Morpheus` | `claude --model <your-strongest>` | ✅ | orchestrator |
| `Neo` | `claude --model <disciplined>` | — | depth |
| `Oracle` | `claude --model <best-reasoner>` | — | escalation |
| `Smith` | `opencode -m <provider/model>` | — | adversarial |
| `Architect` | `opencode -m <provider/large-ctx>` | — | large-context |
| `Link` | `opencode -m <provider/fast-cheap>` | — | triage |

- **Claude Code** takes `--model <alias|id>` (e.g. `opus`, `sonnet`).
- **OpenCode** takes `-m <provider/model>`. List what you have with `opencode models`.
- Only the **Morpheus** preset needs the **Maestro** flag — it's the one that recruits the others.

> **Concrete example (OpenCode Go subscription):**
> - `Smith` → `opencode -m opencode-go/deepseek-v4-pro`
> - `Architect` → `opencode -m opencode-go/kimi-k3`
> - `Link` → `opencode -m opencode/deepseek-v4-flash-free`

## Step 3 — Create the Responsabilidades (Settings → Agentes → Responsabilidades)

One per reviewer, named after the codename. Paste the matching brief into **Instruções**:

| Responsabilidade | Paste from |
|------------------|------------|
| `Neo` | `roles/neo.md` |
| `Smith` | `roles/smith.md` |
| `Architect` | `roles/architect.md` |
| `Oracle` | `roles/oracle.md` |
| `Link` | `roles/link.md` |

Scope them Global or to a specific workspace. *(Optional for a first test — the orchestrator also sends the brief in its dispatch message. But persistent Responsabilidades keep roles clean across runs.)*

## Step 4 — Run it

1. Drag the **Morpheus** preset onto the canvas (it launches with the Maestro flag).
2. Type, in natural language:
   > *"passa o review para o Morpheus na branch feature/xyz"* — or — *"matrix code review of the changes since main"*
3. Morpheus triages the diff, recruits only the crew the risk calls for (Neo + Smith for medium; the full five for high), dispatches them in parallel, and writes a consolidated report.

Watch the canvas: terminals are born connected below Morpheus, each on its own model. Confirm the vendor/model in each terminal's footer.

---

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| Skill doesn't trigger from the phrase | Invoke it explicitly, or check that the skill folder is under a scanned Skills location |
| Morpheus clones itself instead of recruiting OpenCode | The recruit picks an existing preset — make sure the `Smith`/`Architect`/`Link` presets exist with the right `-m` model |
| A reviewer starts on the wrong model | The preset's Comando args set the model; a plain `opencode` uses your default. Pin `-m provider/model` |
| "MCP server needs authentication" | Run `/mcp` in the Morpheus terminal to authenticate the Maestri bridge |
| Fan roars | Expected for the high-risk tier (6 terminals). Model compute runs remote; most local heat is the canvas + local CLIs. Medium tier runs just 2 reviewers |
| Review stalls mid-run / a reviewer hangs | Often a **token/usage quota** hit — large-context models (e.g. Kimi K3 on The Architect) burn quota fast on big diffs. Give that seat a lighter model, reserve it for high risk, or trim its input. See "Tuning the crew" in the README |
