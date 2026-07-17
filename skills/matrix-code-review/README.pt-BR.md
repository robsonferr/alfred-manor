# matrix-code-review 🕶️

🌐 **Português (BR)** · [English](./README.md)

Um **code review multi-modelo e multi-vendor** orquestrado por um único agente, com tema em *Matrix*. Um terminal líder (**Morpheus**) faz a triagem de um git diff, recruta uma tripulação de revisores especialistas — cada um num modelo diferente — roda todos em paralelo e consolida os achados num veredito único, documentando onde eles discordaram.

Feito para rodar no [Maestri](https://www.themaestri.app) (o Maestro Mode recruta terminais reais no canvas), com um fallback só-Claude para qualquer outro lugar. **Agnóstico de linguagem e framework** — revisa qualquer git diff (C#, TypeScript/React, Go, Python…), sem suposições de stack.

## Por quê

Um único modelo revisando os próprios pontos cegos não é uma revisão — é um eco. Famílias de modelos diferentes deixam passar coisas diferentes. Esta skill põe Claude, DeepSeek e um modelo de contexto amplo sobre o mesmo diff, de ângulos distintos, e deixa um bom raciocinador julgar os críticos. O tier pesado só dispara em mudanças de alto risco; diffs do dia a dia recebem uma passada enxuta de dois revisores.

## A tripulação

| Codinome | Papel | Perfil do modelo | Exemplos / sugestões |
|----------|-------|------------------|----------------------|
| 🕶️ **Morpheus** | Orquestrador — triagem, recrutamento, consolidação | Seu modelo mais forte | Claude Opus, Claude Fable, GPT‑5 |
| ☎️ **Link** | Triagem — passada rápida e barata | Um modelo rápido/barato | DeepSeek Flash, Claude Haiku, Gemini Flash |
| 🥋 **Neo** | Depth — a revisão principal | Um revisor disciplinado | Claude Sonnet, GPT‑5, Gemini Pro |
| 🕴️ **Agent Smith** | Adversarial — o cético independente | Um vendor diferente do Neo | DeepSeek V4 Pro, GPT‑5, Grok |
| 🏛️ **The Architect** | Contexto amplo — contratos cross-file | Um modelo de janela grande | Kimi K3, Gemini Pro (1M) |
| 🔮 **The Oracle** | Escalação — a palavra final sobre os 🔴 | Seu melhor raciocinador | Claude Opus, GPT‑5 |

Codinome = um papel estável. O modelo por baixo é trocável — os exemplos são só ilustrativos, use o que você tiver.

## Como funciona

```
Morpheus (triagem) → recruta a tripulação por risco → despacha em paralelo
   → cada revisor trabalha independente → Morpheus consolida + resolve divergências
   → um relatório: achados, fontes, disputas, veredito
```

- A **triagem** dá nota ao diff (tamanho, superfície de segurança, migrations, contratos…) → baixo / médio / alto.
- O **despacho** escala ao risco: baixo = só o Link; médio = Neo + Smith; alto = os cinco.
- A **síntese** cruza os achados, e o Oracle confirma ou refuta os críticos.

Dois modos de execução:
- **Modo Maestri** — Morpheus recruta terminais reais (cada um num CLI/modelo diferente) via Maestro Mode, coordenando por notas compartilhadas. Veja [`SETUP-maestri.md`](./SETUP-maestri.md).
- **Modo fallback** — sem Maestri? Ele dispara sub-agentes Claude via a tool `Agent`. Múltiplas perspectivas, um só vendor.

## Arquivos

```
SKILL.md            → a definição da skill (contrato, triagem, tripulação, síntese)
checks.md           → checklist genérico de review (SEC/COR/PERF/ARCH/API/QUAL/TEST)
SETUP-maestri.md    → passo a passo da configuração no Maestri (uma vez)
roles/              → o brief de cada revisor
  link.md · neo.md · smith.md · architect.md · oracle.md
```

## Instalação

Coloque a pasta no diretório de skills do seu agente (ex.: `~/.claude/skills/matrix-code-review`). No Maestri, mantenha-a sob um dos locais de skill escaneados para que o orquestrador a enxergue.

## Início rápido

**No Maestri:** faça o setup único de [`SETUP-maestri.md`](./SETUP-maestri.md) e, num terminal Maestro, diga:
> *"passa o review para o Morpheus na branch feature/xyz"* — ou — *"matrix code review das mudanças desde a main"*

**Em qualquer outro lugar (Claude):** invoque a skill apontando para uma base:
> *"matrix code review, base = main"*

Opcionalmente passe um arquivo de spec/PRD/issue para os revisores verificarem os critérios de aceite, e `--out <caminho>` para posicionar o relatório. A saída padrão é `./matrix-review-<data>.md`.

## Ajustando a tripulação (modelos & custo)

O **mapa codinome → modelo é um botão, não uma regra.** Cada papel é agnóstico de modelo — ligue os CLIs/modelos que você usa, mantenha os codinomes, e **experimente para ver o que rende melhor no seu stack**. Alguns pontos que valem saber:

- **A diversidade de vendor é o prêmio, mas é opcional.** Dá para rodar a tripulação inteira num só vendor — 100% Anthropic, 100% OpenAI, o que for — e funciona. Você só abre mão do ângulo cross-vendor: modelos de famílias *diferentes* deixam passar coisas diferentes, e uma tripulação de mesma família compartilha pontos cegos. Misture vendors nos papéis que mais importam (Neo × Smith é o par-chave) se quiser essa cobertura.
- **Modelos de raciocínio pesado / contexto amplo são poderosos, porém famintos de token.** O assento do Architect (ex.: **Kimi K3**) traz raciocínio sério e uma janela enorme — ótimo para bugs de contrato cross-file — mas um diff grande jogado nele pode **queimar uma cota de uso rapidinho e travar o run**. Se isso te pegar:
  - reserve o revisor de contexto amplo para diffs realmente grandes / de alto risco (a triagem já o restringe a alto risco);
  - ou coloque um modelo mais leve nesse assento;
  - ou reduza o que você manda para ele (só os arquivos tocados + contexto imediato, não o repo inteiro).
- **Os tiers de triagem existem para controlar custo.** Mudanças de baixo risco rodam um revisor barato; o tier caro de cinco modelos só dispara em alto risco. Ajuste os limiares em `SKILL.md` §3 ao seu gosto.

- **É agnóstico de linguagem — enriqueça o `checks.md` para ter profundidade.** Os revisores já leem C#, TypeScript/React, Go, Python etc.; aponte para qualquer repo e funciona. As categorias do `checks.md` são neutras de linguagem, então o jeito de deixar uma revisão *afiada* para uma stack é adicionar ali (e/ou em `roles/*.md`) as boas práticas dela. Você não ensina a linguagem ao modelo — você foca a atenção dele. Exemplos:
  - **React/TS**: regras de hooks, array de dependências de effect, `key` em listas, `dangerouslySetInnerHTML`/XSS, memoização, acessibilidade, tamanho de bundle.
  - **C#/.NET**: `async`/`await` e `ConfigureAwait`, `IDisposable`/`using`, padrões de query do EF (N+1, tracking), nullable reference types.

Todo o resto também é ajustável: as categorias em **`checks.md`** e o brief de cada revisor em **`roles/*.md`**.

## Licença

MIT.
