<p align="center">
  <img src="./assets/alfred-banner.jpeg" alt="Alfred's Manor" width="50%">
</p>

# Alfred's Manor 🎩

🌐 **English** · [Português (BR)](./README.pt-BR.md)

A curated house of agent skills — each one convenes a themed **cast** of specialists to do one job well. Model-agnostic, self-contained, MIT-licensed. *Kept by Robson & Alfred.*

## The skills

| Skill | What it does | The cast |
|-------|--------------|----------|
| [**matrix-code-review**](./skills/matrix-code-review/) | Multi-model, multi-vendor code review orchestrated on one canvas | Morpheus · Neo · Agent Smith · The Architect · The Oracle · Link |
| [**council**](./skills/council/) | Deliberate a hard, costly, or uncertain decision — five lenses, one verdict | Elrond · Gandalf · Boromir · Aragorn · Sam · Frodo |

*Every skill here assembles a crew. That's the house style.*

## The idea

Most agent skills are a single voice following a checklist. The ones in this house do something different: they **convene a cast** — each member locked into one lens or role — and let a chair weigh the room before answering. A code review becomes a crew of different-vendor models catching each other's blind spots; a hard decision becomes a council that argues before it rules.

Two consequences worth knowing:

- **Model-agnostic.** Every role names a *profile* ("your strongest model", "a different vendor"), never a fixed model. Wire in whatever you run.
- **Self-contained.** Each skill folder carries its own docs, roles, and setup. Take one, leave the rest.

## Install

These are plain-Markdown skills — no build step. Copy a skill folder into your agent's skills directory (e.g. `~/.claude/skills/`) and follow that skill's own setup for anything extra (some bring subagents or a canvas setup).

Each skill documents itself:

- [**matrix-code-review**](./skills/matrix-code-review/README.md) — reviews any git diff; runs best on [Maestri](https://www.themaestri.app), falls back to Claude-only.
- [**council**](./skills/council/README.md) — needs a harness where the main thread can spawn subagents (e.g. Claude Code).

## Use

Invoke a skill by its trigger phrase or slash name — each README documents its own. For example:

> *"convene the council: rewrite or patch the billing service?"*
> *"matrix code review of the changes since main"*

## Coming later

The manor's namesake — **Alfred**, the butler persona that curates all this — may move in too, as a clean, parameterized persona. Not yet: a persona ships only after a line-by-line audit for anything personal.

## Support

If you find this Skills useful, consider buying me a coffee:

<a href="https://buymeacoffee.com/robsonferr" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" width="180"></a>

## License

[MIT](LICENSE)

## Author

Built with 🤟 by Robson & Alfred

