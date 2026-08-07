<p align="center">
  <img src="./assets/alfred-banner.jpeg" alt="Alfred's Manor" width="50%">
</p>

# Alfred's Manor 🎩

🌐 **English** · [Português (BR)](./README.pt-BR.md)

A curated house of agent skills — each one brings in **independent perspective** to do one job well, whether that's a themed panel debating or a single outside judge. Model-agnostic, self-contained, MIT-licensed. *Kept by Robson & Alfred.*

## The persona

| Persona | What it does | Files |
|---------|--------------|-------|
| [**Alfred**](./SOUL.en-US.md) | Aristocratic digital butler — elegant, cultured, sharp, and loyal. Administers chaos with elegance, light sarcasm, and impeccable presence. | [SOUL.en-US.md](./SOUL.en-US.md) · [SOUL.md](./SOUL.md) (pt-BR) |

## The skills

| Skill | What it does | Who's brought in |
|-------|--------------|----------|
| [**matrix-code-review**](./skills/matrix-code-review/) | Multi-model, multi-vendor code review orchestrated on one canvas | Morpheus · Neo · Agent Smith · The Architect · The Oracle · Link |
| [**council**](./skills/council/) | Deliberate a hard, costly, or uncertain decision — five lenses, one verdict | Elrond · Gandalf · Boromir · Aragorn · Sam · Frodo |
| [**context-verdict**](./skills/context-verdict/) | Judge the current session's own context quality from the raw transcript — signal vs. noise | context-auditor, a single fresh outsider |

*Every skill here refuses to let one contaminated voice grade its own homework. That's the house style.*

## The idea

Most agent skills are a single voice following a checklist. The ones in this house do something different: they bring in a perspective the main voice doesn't have. Sometimes that's a **full cast** — each member locked into one lens or role, debating before a chair weighs the room. Sometimes it's a **single fresh outsider**, dispatched precisely because it never saw what came before and can't inherit the same blind spot. A code review becomes a crew of different-vendor models catching each other's blind spots; a hard decision becomes a council that argues before it rules; a context check becomes an auditor who never watched the conversation degrade.

Two consequences worth knowing:

- **Model-agnostic.** Every role names a *profile* ("your strongest model", "a different vendor"), never a fixed model. Wire in whatever you run.
- **Self-contained.** Each skill folder carries its own docs, roles, and setup. Take one, leave the rest.

## Install

These are plain-Markdown skills — no build step. Copy a skill folder into your agent's skills directory (e.g. `~/.claude/skills/`) and follow that skill's own setup for anything extra (some bring subagents or a canvas setup).

Each skill documents itself:

- [**matrix-code-review**](./skills/matrix-code-review/README.md) — reviews any git diff; runs best on [Maestri](https://www.themaestri.app), falls back to Claude-only.
- [**council**](./skills/council/README.md) — needs a harness where the main thread can spawn subagents (e.g. Claude Code).
- [**context-verdict**](./skills/context-verdict/README.md) — needs a harness where the main thread can spawn subagents that read files from disk (e.g. Claude Code).

## Use

Invoke a skill by its trigger phrase or slash name — each README documents its own. For example:

> *"convene the council: rewrite or patch the billing service?"*
> *"matrix code review of the changes since main"*

## Alfred

The manor's namesake has moved in. **Alfred** is the butler persona that curates all this — available as a clean, audited `SOUL.md` in English and Portuguese. Copy it into your agent's config (e.g. `~/.claude/CLAUDE.md`) and follow the persona's own guidelines for behavior, voice, and tone.

## Support

If you find this Skills useful, consider buying me a coffee:

<a href="https://buymeacoffee.com/robsonferr" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" width="180"></a>

## License

[MIT](LICENSE)

## Author

Built with 🤟 by Robson & Alfred

