# context-verdict 🔎 — o auditor externo

🌐 **Português (BR)** · [English](./README.md)

Uma skill de especialista único que julga **a qualidade do próprio contexto da sessão atual** — sinal vs. ruído — a partir do transcript bruto em disco, imune à degradação da própria sessão.

## Por quê

Um modelo avaliando a própria janela de contexto tem um ponto cego: à medida que a janela enche e a qualidade degrada, a *autoavaliação* degrada junto — um modelo cansado também julga mal o próprio cansaço. Esta skill contorna isso disparando um subagente novo (`context-auditor`) que nunca viu a conversa, lê o transcript `.jsonl` como bytes, calcula métricas determinísticas e devolve um veredito baseado em evidência.

É o complemento qualitativo de um medidor de contexto em %: o medidor diz *quão cheio*, o auditor diz *quanto disso ainda vale a pena manter*.

> ⚠️ **Feita para o Claude Code especificamente — mais do que as outras skills desta casa.** O auditor lê o transcript da sessão como um arquivo `.jsonl` na convenção de caminho em disco do Claude Code, e o arquivo de agente usa o frontmatter de subagente do Claude Code (`name`/`tools`/`model`/`effort`). Portar para Codex, OpenCode, Grok ou outro harness exige reescrever *como o transcript é localizado e parseado* (Step 1 do `SKILL.md`), não só trocar um valor de config — confira se seu harness sequer persiste um transcript bruto e "grepável" antes de adotar esta skill.

## O elenco

| Papel | Função |
|-------|--------|
| 🔎 **context-auditor** | Lê o transcript JSONL, calcula taxa de ruído / densidade de erro / sinais de "thrash", amostra os maiores dumps e decide 🟢/🟡/🔴 |
| 🎩 **a thread principal** | Resolve o caminho do transcript, declara o objetivo atual, dispara o auditor, relata o veredito na própria voz |

## Como funciona

```
Declare o objetivo atual (uma linha)
   → dispare context-auditor com o caminho do transcript + objetivo
   → o auditor calcula métricas em disco (jq/wc/grep, nunca um Read completo)
   → o auditor amostra os maiores dumps de tool_result, julga relevância
   → veredito: 🟢 LIMPO / 🟡 RUIDOSO / 🔴 CRÍTICO + justificativa
   → a thread principal relata, sinalizando qualquer divergência com sua própria impressão
```

## Régua do veredito

- 🟢 **limpo** — volume obsoleto/ruído abaixo de ~40%, poucos ou nenhum erro, sem thrash. Siga trabalhando.
- 🟡 **ruidoso** — ~40–70% obsoleto, ou vários comandos falhos/explorações gastas dominando a janela. Vale ponderar uma limpeza agora.
- 🔴 **crítico** — acima de ~70% obsoleto, ou loops de erro repetidos / thrash pesado. Limpar antes da próxima task, sem debate.

Os limiares são orientação, não lei — uma janela aparentemente ruidosa mas ainda totalmente relevante ao objetivo atual pode continuar 🟢; um transcript pequeno cheio de becos sem saída pode ser 🔴.

## Arquivos

```
SKILL.md                                    → o protocolo de disparo (thread principal)
agents/context-verdict/context-auditor.md   → o auditor (subagente)
SETUP.md                                    → instalação (skill + agente) e pré-requisitos
```

## Instalação

Requer um harness onde a thread principal pode disparar subagentes e o subagente pode ler arquivos em disco (ex.: Claude Code). Copie a skill para o seu diretório de skills e o agente para o diretório de agentes — veja o [`SETUP.md`](./SETUP.md).

## Uso

> *"context-verdict"* / *"qual a qualidade do contexto?"* / numa fronteira de task, antes de decidir se limpa.

Dispare em fronteiras de task — ao terminar uma task, perto de um limiar de % de contexto que você mesmo definiu, ou antes de uma próxima task pesada — nunca no meio de uma task, já que o veredito só é preciso até o momento em que você o dispara.

## Personalização

- **`agents/context-verdict/context-auditor.md`** — reajuste os limiares da régua, as métricas calculadas, ou o tier de modelo (vem como Sonnet, effort baixo — barato por design).
- **Idioma** — a saída do veredito vem em pt-BR por padrão; traduza o bloco de saída do auditor e o texto de relato da skill para outro idioma.
- **Resolução do caminho do transcript** — o Step 1 do `SKILL.md` assume a convenção em disco do Claude Code; ajuste se seu harness for diferente.

## Licença

MIT.
