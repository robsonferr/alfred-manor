# council 🧙 — the Council of Elrond

🌐 **English** · [Português (BR)](./README.pt-BR.md)

A decision-making skill for **hard, costly, irreversible, or genuinely uncertain** calls. Instead of one voice, it convenes five councilors — each locked into a distinct thinking lens — debates them, attacks the consensus, and returns a single weighed verdict.

Themed on the Council of Elrond from *The Lord of the Rings*.

## Why

A single model deliberating with itself smooths its own biases into a confident average. Five councilors, each forced 100% into one lens and read against each other, surface the disagreement a lone answer hides. The chair (Elrond) then **attacks the consensus** before ruling — so you get a decision, its biggest risk, a first step, and where the council split.

## The council

| Councilor | Lens | Model tier |
|-----------|------|-----------|
| 🧙 **Gandalf** | First principles — the real problem under the framing | strongest |
| ⚔️ **Boromir** | The contrarian — the weakest link + the bold bet (and its price) | mid |
| 👑 **Aragorn** | The expansionist — the ignored upside, the bigger version | mid |
| 🌱 **Sam** | The outsider — common sense, no context, says the obvious | cheapest |
| 💍 **Frodo** | The executor — the one concrete next step | mid |
| 🏛️ **Elrond** | The chair — convenes, weighs, attacks consensus, rules | the main thread |

## How it works

```
Frame the question → convene the 5 councilors in parallel
   → Gandalf / Boromir / Aragorn / Frodo get context; Sam gets none (outsider)
   → read all five → attack the consensus → weigh → verdict
```

Elrond is the skill running on your main thread — a subagent can't spawn subagents, so the chair lives there. **Sam is deliberately blinded** to context so his common sense stays uncontaminated by the experts.

The verdict returns: **decision · biggest risk to watch · first step · where the council diverged · confidence (n/10)**.

## Files

```
SKILL.md              → the chairing protocol
agents/council/       → the five councilors (subagents)
SETUP.md              → install (skill + agents) and prerequisites
```

## Install

Requires a harness where the main thread can spawn subagents (e.g. Claude Code). Copy the skill into your skills directory and the five agents into your agents directory — see [`SETUP.md`](./SETUP.md).

## Use

> *"convene the council: should we rewrite the billing service or patch it?"*

The assistant frames the question, fires the five councilors, and returns a single weighed verdict — dissent preserved, not smoothed over.

## Customise

- **`agents/council/*.md`** — retune any councilor's voice or lens.
- **Model tiers** — swap the `model` in each councilor's frontmatter for what you run.

## License

MIT.
