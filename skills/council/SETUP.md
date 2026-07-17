# Setup — installing the Council

The Council runs on a harness where **the main thread can spawn subagents** (e.g. Claude Code). It has two parts, installed in separate locations.

## 1. The skill

Copy `SKILL.md` into your skills directory, under a `council/` folder:

```
~/.claude/skills/council/SKILL.md
```

## 2. The councilors (subagents)

Copy the five agent files into your agents directory:

```
~/.claude/agents/council/councilor-gandalf.md
~/.claude/agents/council/councilor-boromir.md
~/.claude/agents/council/councilor-aragorn.md
~/.claude/agents/council/councilor-sam.md
~/.claude/agents/council/councilor-frodo.md
```

These register as subagent types (`councilor-gandalf`, etc.) that the skill convenes. Paths may differ by harness — put them wherever your tool discovers subagents.

## 3. Verify

Ask your assistant to "convene the council" on a real decision. It should assume the role of Elrond, fire the five councilors in parallel, and return a single verdict.

---

## Notes

- **No Elrond agent file.** Elrond is the *chair* — played by the main thread running the skill, not a subagent. (A subagent can't spawn subagents; the chair must, so it lives on the main thread.)
- **Sam is deliberately blinded.** His frontmatter disallows file/context tools so he reacts only to the question. Keep it that way — the outsider's ignorance is the feature, not a bug.
- **Model tiers are a suggestion.** Each councilor declares a `model` in its frontmatter — Gandalf on the strongest, Sam on the cheapest, the rest mid-tier. Adjust to taste or to what you have; the roles work on any model.
- **Give context selectively.** The chair sends the real context to Gandalf, Boromir, Aragorn, and Frodo — but only the bare question to Sam. Preserving that asymmetry is what makes Sam's common-sense check honest.
