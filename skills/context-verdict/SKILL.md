---
name: context-verdict
description: Dispatch an external auditor subagent to judge the current session's context quality (signal vs. noise) from the transcript on disk, returning a 🟢/🟡/🔴 verdict to feed PonderarLimpeza(). Use at task boundaries (/done-task, end of phase), when the context meter is near the 60% threshold, when the next task is heavy, or when the user asks "qualidade do contexto?", "/context-verdict", "auditar contexto", "veredito de contexto".
---

# Context Verdict

Audits the **current session's** context quality via an external subagent (`context-auditor`, Sonnet, effort low). The auditor reads the raw transcript JSONL from disk — evidence-based judgment, immune to the main session's own degradation ("a degraded model also degrades at judging its own degradation").

## Why a subagent

The main agent's inline self-assessment gets less reliable as the window fills. The auditor starts fresh, sees the transcript as bytes on disk, and computes deterministic metrics (noise ratio, error density, thrash) before judging. It is the qualitative instrument; the user's context-% meter is the quantitative one. Neither replaces the other.

## Steps

### 1. Resolve the transcript path

- **Session ID**: the UUID segment of the scratchpad directory path provided in the system prompt.
- **Project transcript dir**: `~/.claude/projects/<encoded-cwd>/` where `<encoded-cwd>` is the primary working directory with every `/` (and `.` if present) replaced by `-` (e.g. `/Users/x/proj` → `-Users-x-proj`).
- Transcript: `<dir>/<session-id>.jsonl`. Verify it exists (`ls -la`). Fallback if the ID cannot be derived: the most recently modified `.jsonl` in the directory.

### 2. State the current objective

Write ONE line describing what this session is currently working on / about to do next (e.g. "planejando sprint-68; próxima ação: iniciar T-007 da feature X"). The auditor needs it to judge which heavy content is still relevant vs. spent.

### 3. Dispatch the auditor

Call the `Agent` tool:
- `subagent_type`: `context-auditor`
- `run_in_background`: `false` (the verdict is needed now; it takes ~1 min)
- `prompt`: include `TRANSCRIPT: <absolute path>` and `CURRENT_OBJECTIVE: <the line from step 2>`.

Do NOT summarize the session for the auditor beyond the objective line — the whole point is that it judges from raw evidence, not from the main session's (possibly degraded) self-report.

### 4. Relay the verdict (user-facing, in the project language / pt-BR)

Present in the assistant's own voice, concise:

1. The verdict line: 🟢 LIMPO / 🟡 RUIDOSO / 🔴 CRÍTICO + the auditor's justification (verbatim or lightly polished — do not soften a 🔴).
2. The top heavy items and whether they are still relevant.
3. The recommendation mapped to the user's algorithm:
   - 🔴 → limpar antes da próxima task, sem debate.
   - 🟡 → rodar PonderarLimpeza() agora: próxima task é grande? preferiria cérebro limpo? qualquer sim → limpar.
   - 🟢 → seguir; limpeza pode esperar mesmo com o medidor de % apertado.

If the main agent's own inline impression disagrees with the auditor's verdict, say so explicitly — the divergence is information, and the auditor's evidence-based view wins by default.

## Notes

- Cheap by design: the auditor is Sonnet at `effort: low`; the heavy lifting is deterministic (`jq`/`wc`/`grep`), the model only issues the final judgment.
- The verdict covers everything up to the moment of dispatch — fire it at the END of a task (after /done-task), never at the start, or the verdict is stale by the time it is needed.
