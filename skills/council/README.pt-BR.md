# council 🧙 — o Conselho de Elrond

🌐 **Português (BR)** · [English](./README.md)

Uma skill de tomada de decisão para escolhas **difíceis, caras, irreversíveis ou de genuína incerteza**. Em vez de uma voz só, ela convoca cinco conselheiros — cada um travado numa lente de pensamento distinta — debate entre eles, ataca o consenso e devolve um veredito único e pesado.

Com tema no Conselho de Elrond, de *O Senhor dos Anéis*.

## Por quê

Um único modelo deliberando consigo mesmo suaviza os próprios vieses numa média confiante. Cinco conselheiros, cada um forçado 100% numa lente e lidos uns contra os outros, expõem a discordância que uma resposta solitária esconde. O presidente (Elrond) então **ataca o consenso** antes de decidir — então você recebe uma decisão, o maior risco dela, um primeiro passo, e onde o conselho divergiu.

## O conselho

| Conselheiro | Lente | Tier de modelo |
|-------------|-------|----------------|
| 🧙 **Gandalf** | Primeiros princípios — o problema real sob o enquadramento | mais forte |
| ⚔️ **Boromir** | O contraditor — o elo mais fraco + a aposta ousada (e seu preço) | médio |
| 👑 **Aragorn** | O expansionista — o upside ignorado, a versão maior | médio |
| 🌱 **Sam** | O forasteiro — senso comum, sem contexto, diz o óbvio | mais barato |
| 💍 **Frodo** | O executor — o único próximo passo concreto | médio |
| 🏛️ **Elrond** | O presidente — convoca, pesa, ataca o consenso, decide | a thread principal |

## Como funciona

```
Enquadre a questão → convoque os 5 conselheiros em paralelo
   → Gandalf / Boromir / Aragorn / Frodo recebem contexto; Sam não recebe (forasteiro)
   → leia os cinco → ataque o consenso → pese → veredito
```

Elrond é a skill rodando na sua thread principal — um subagente não pode disparar subagentes, então a presidência vive ali. **Sam é cegado de propósito** ao contexto, para que o senso comum dele não seja contaminado pelos especialistas.

O veredito retorna: **decisão · maior risco a vigiar · primeiro passo · onde o conselho divergiu · confiança (n/10)**.

## Arquivos

```
SKILL.md              → o protocolo de presidência
agents/council/       → os cinco conselheiros (subagentes)
SETUP.md              → instalação (skill + agentes) e pré-requisitos
```

## Instalação

Requer um harness onde a thread principal pode disparar subagentes (ex.: Claude Code). Copie a skill para o seu diretório de skills e os cinco agentes para o diretório de agentes — veja o [`SETUP.md`](./SETUP.md).

## Uso

> *"convoca o conselho: a gente reescreve o serviço de billing ou remenda?"*

O assistente enquadra a questão, dispara os cinco conselheiros e devolve um veredito único e pesado — com a dissidência preservada, não varrida para baixo do tapete.

## Personalização

- **`agents/council/*.md`** — reajuste a voz ou a lente de qualquer conselheiro.
- **Tiers de modelo** — troque o `model` no frontmatter de cada conselheiro pelo que você roda.

## Licença

MIT.
