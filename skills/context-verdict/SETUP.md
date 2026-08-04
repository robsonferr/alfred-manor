# Setup — installing Context Verdict

Requires a harness where the main thread can spawn subagents (e.g. Claude Code) and where subagents can read files from disk (`Bash`, `Read`, `Grep`).

## 1. The skill

Copy `SKILL.md` into your skills directory, under a `context-verdict/` folder:

```
~/.claude/skills/context-verdict/SKILL.md
```

## 2. The auditor (subagent)

Copy the agent file into your agents directory:

```
~/.claude/agents/context-auditor.md
```

This registers as the `context-auditor` subagent type that the skill dispatches.

## 3. Verify

Mid-session, ask your assistant to run "/context-verdict" (or whatever trigger phrase you set — see the skill's own description). It should:

1. Resolve the current session's transcript path.
2. Dispatch `context-auditor` with that path and a one-line objective.
3. Relay a 🟢/🟡/🔴 verdict with justification and the heaviest context items.

## Notes

- **Cheap by design.** The auditor never reads the whole transcript — only `jq`/`wc`/`grep` metrics plus small samples. Keep it that way; a full-file `Read` defeats the point of running this cheaply and often.
- **Transcript path convention is Claude-Code-specific.** Step 1 of `SKILL.md` assumes `~/.claude/projects/<encoded-cwd>/<session-id>.jsonl`. If your harness stores transcripts elsewhere, adjust that step — the rest of the skill (metrics, verdict rubric) is harness-agnostic.
- **Output defaults to pt-BR** (verdict lines, justification block). The metrics themselves are language-agnostic — translate the auditor's output template and the skill's relay wording if you want a different language.
- **Fire at task boundaries, not mid-task.** The verdict covers everything up to the moment of dispatch — running it at the start of a task makes it stale by the time you need it.
