---
name: matrix-code-review
description: Multi-model orchestrated code review, themed on The Matrix. Triggers include "matrix code review", "review na Matrix", "passa o review para o Morpheus", "code review multi-agente". A Maestro terminal running the orchestrator (Morpheus) triages risk, recruits specialist reviewer terminals — Neo (depth), Agent Smith (adversarial), The Architect (large-context), The Oracle (escalation), Link (triage), each a different model/vendor — dispatches them in parallel, consolidates findings, and documents divergences. Falls back to Claude-only sub-agents when Maestri is unavailable. Project-agnostic: reviews any git diff.
license: MIT
---

This skill runs a structured code review using **multiple models in parallel**, each looking at the same diff from a different angle. It is project-agnostic — it reviews a git diff and (optionally) a spec file. The orchestrator, **Morpheus**, triages risk, recruits the right crew, dispatches focused briefs, and consolidates everything into a single verdict.

> **The crew.** Each reviewer is a codename (a stable role) backed by a model (swappable — use whatever you have).
>
> | Codename | Role | Typical model |
> |---|---|---|
> | **Morpheus** | Orchestrator | Your strongest model (e.g. Claude Fable/Opus) |
> | **Link** | Triage | A fast, cheap model (e.g. DeepSeek Flash) |
> | **Neo** | Depth | A disciplined reviewer (e.g. Claude Sonnet) |
> | **Agent Smith** | Adversarial | A different-vendor model (e.g. DeepSeek Pro) |
> | **The Architect** | Large-context | A large-window model (e.g. Kimi K3) |
> | **The Oracle** | Escalation | Your best reasoner (e.g. Claude Opus) |

> **How it runs.** Two modes. **Maestri mode** (best): the orchestrator runs in a [Maestri](https://www.themaestri.app) **Maestro** terminal and recruits real terminals — each a different CLI/model — via the `maestri-manager` skill, coordinating through the `maestri` skill (messages + shared notes). Setup: see `SETUP-maestri.md`. **Fallback mode**: no Maestri → the orchestrator spawns Claude sub-agents via the `Agent` tool (multiple *perspectives*, single *vendor*).

---

## 1. Invocation & contract

### Inputs (all optional except the diff)

The skill figures these out from the invocation, or asks:

| Input | How it's obtained | Default |
|-------|-------------------|---------|
| **Base ref** | Argument, e.g. `main`, a tag, a commit | The default branch (`git merge-base` against `main`/`master`) |
| **Head ref** | Argument | Current branch / working tree |
| **Spec** | Optional path to a PRD / issue / acceptance-criteria file | none — reviews the diff on its own merits |
| **Change name** | Argument, or inferred from the branch name | branch name |
| **Output path** | `--out` argument | `./matrix-review-<YYYY-MM-DD>.md` in the repo root |

The diff under review is `git diff <base>...<head>`.

### Output

A single consolidated markdown report at the output path, containing: the triage note, a spec-coverage table (if a spec was given), a findings table with a `Source` column (which reviewer), a divergences section, a consensus summary, and an actions list. Severity and verdict per `checks.md`.

### Checks

All reviewers work from `checks.md` (categories SEC / COR / PERF / ARCH / API / QUAL / TEST). It is generic — adapt it to your stack if you like.

---

## 2. The crew

| Codename | Role | Vendor CLI (Maestri preset) | When to recruit |
|----------|------|-----------------------------|-----------------|
| **Link** | Triage | fast/cheap model | Low-risk (solo), or to confirm triage on medium/high |
| **Neo** | Depth | disciplined reviewer | Every change (medium and above) |
| **Agent Smith** | Adversarial | different vendor | Every change (medium and above) |
| **The Architect** | Large-context | large-window model | High-risk, cross-file, big diffs (>15 files) |
| **The Oracle** | Escalation | best reasoner | High-risk only — final authority on 🔴 |

> **Why the Architect gets the large-context seat and not escalation.** A large-window model *understands* cross-file contracts rather than only matching patterns — ideal for the wide-net finder role, and its output is filtered downstream. The escalation seat wants the lowest false-positive rate and cleanest instruction-following. Keep the disciplined reasoner as the Oracle.

### Dispatch matrix

| Triage result | Link | Neo | Agent Smith | The Architect | The Oracle |
|---------------|:-:|:-:|:-:|:-:|:-:|
| **Low** | ✅ (as reviewer) | ❌ | ❌ | ❌ | ❌ |
| **Medium** | ✅ (triage only) | ✅ | ✅ | ❌ | ❌ |
| **High** | ✅ (triage only) | ✅ | ✅ | ✅ | ✅ |

---

## 3. Triage (Phase 0)

Morpheus triages using its full context — it does **not** delegate this step. Sum the risk score:

| Factor | Low (+0) | Medium (+1) | High (+2) |
|--------|----------|-------------|-----------|
| Change nature | refactor / bugfix | — | new feature / behaviour change |
| Files touched | ≤3 | 4–15 | >15 |
| Lines changed | ≤100 | 101–500 | >500 |
| Touches auth / payments / data store? | No | Indirectly | Directly |
| Touches shared contracts? | No | Internal API | Public API / cross-service |
| Migration present? | No | Additive | Destructive (drop/alter/rename) |
| New runtime dependencies? | No | Dev-only | Runtime |
| Security-sensitive area? | No | Adjacent | Directly (authz, tenancy, crypto) |

| Total | Risk | Crew |
|-------|------|------|
| 0–2 | **Low** | Link only |
| 3–7 | **Medium** | Neo + Agent Smith (Link confirms triage) |
| 8+ | **High** | Neo + Agent Smith + The Architect + The Oracle |

Write the triage note (classification, score, factor table, recruited crew) into the report.

---

## 4. Execution modes

### 4.1 Maestri mode (parallel — primary)

Requires a **Maestro** terminal (the `maestri-manager` skill available). See `SETUP-maestri.md` for one-time setup (presets + Responsabilidades).

1. **Take inventory** via the `maestri` skill — reuse any fitting reviewer already on the canvas.
2. **Recruit** each missing crew member via `maestri-manager`: open the terminal from its **preset** (the vendor CLI at the right model) and assign its **Responsabilidade** (the `roles/<codename>.md` brief). Degrade per 4.3 if a vendor isn't set up.
3. **Dispatch in parallel** via `maestri`: a **shared briefing note** for the common payload (diff, spec excerpts, `checks.md`) + a direct message for anything reviewer-specific.
4. **Collect** each terminal's output (reply or the section it appended to a shared results note).
5. **Dismiss** terminals (optional) — or keep them warm for the next review.

### 4.2 Fallback mode (Claude-only, no Maestri)

Spawn one sub-agent per crew member via the **`Agent`** tool, passing the same inputs and the matching `roles/<codename>.md` brief. Sub-agents run **Claude models only** — multiple perspectives, single vendor. State this in the report. Each runs read-only and returns findings.

### 4.3 Degradation

If a required non-Claude vendor isn't configured, recruit a Claude preset instead, keep the same Responsabilidade, and record a warning in the report (`degraded_from`). Never abort a review just because a vendor is absent — a single-vendor review beats no review.

---

## 5. Reviewer briefs

Canonical brief per crew member lives in `roles/<codename>.md`. Morpheus sends each reviewer the diff, the relevant `checks.md` categories, and (if provided) the spec.

- **Link** (`roles/link.md`) — fast surface scan, 5-line verdict.
- **Neo** (`roles/neo.md`) — full-depth review; spec coverage + all categories; tag `[Neo]`.
- **Agent Smith** (`roles/smith.md`) — independent adversarial pass, does **not** see Neo's output; security/edge/concurrency/contracts; tag `[Smith]`.
- **The Architect** (`roles/architect.md`) — cross-file contracts, duplication, mismatches; tag `[Architect]`.
- **The Oracle** (`roles/oracle.md`) — reviews the *findings*, confirms/refutes each 🔴, catches misses; tag `[Oracle]`.

---

## 6. Synthesis (Phase 2)

1. **Collect** all findings, tagged by codename. **Normalise** formats — recruited terminals are autonomous and may drift from the requested layout; reshape each into the common row.
2. **Cross-reference**: mark agreements and disputes.
3. **Resolve divergences:**

| Scenario | Action |
|----------|--------|
| Neo + Smith agree on 🔴 | Keep as 🔴 (high confidence) |
| Neo found 🔴, Smith didn't | Escalate to the Oracle if recruited; else keep as 🟡 |
| Smith found 🔴, Neo didn't | Keep as 🔴 (different vendor caught it) |
| The Oracle confirmed | Promote to 🔴 if not already |
| The Oracle disputed | Downgrade one level, note the dispute |

4. **Write the report** to the output path: triage note · spec coverage (if any) · findings table with `Source` column · divergences · consensus (unanimous? disputes? degraded reviewers?) · actions list (each tagged with its source codename). Verdict: *Approved* (no 🔴) / *Changes requested* (≥1 🔴).

---

## 7. Procedure

1. **Resolve inputs** (Section 1): base/head refs, optional spec, change name, output path. Compute the diff; count files and lines.
2. **Triage** (Section 3): score, classify, decide the crew.
3. **Choose mode** (Section 4): Maestri if available, else fallback. Recruit/spawn only the crew from step 2; degrade as needed.
4. **Dispatch** the diff + briefs + `checks.md` categories.
5. **Triage confirmation** (if Link ran on medium/high): if Link signals higher risk than assumed, recruit the next tier.
6. **Synthesise** (Section 6): normalise, cross-reference, resolve divergences.
7. **Write the report** and tell the user the verdict + a findings summary. Note any degraded reviewers. In Maestri mode, offer to dismiss or keep the crew.

---

## 8. Maestri setup

One-time configuration (presets + Responsabilidades + Maestro Mode) is documented in **`SETUP-maestri.md`**. In short: create one **preset** per vendor CLI (the model is pinned via the command args), one **Responsabilidade** per codename (paste the `roles/*.md` brief), and mark the orchestrator terminal as **Maestro**. Recruiting = preset + Responsabilidade.

---

## 9. Error handling

| Scenario | Action |
|----------|--------|
| No Maestro / `maestri-manager` unavailable | Fall back to Claude-only mode; note it in the report |
| Required vendor not configured | Degrade to a Claude preset, keep the Responsabilidade, warn (4.3) |
| A reviewer doesn't respond | Warn, proceed with the rest, mark it `no-response` |
| A reviewer returns malformed output | Normalise during synthesis; if unusable, mark `no-response` |
| No spec provided | Review the diff on its own merits — skip the spec-coverage table |
| Triage score = 0 | Recruit Link only |
