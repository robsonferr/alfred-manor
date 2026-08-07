<p align="center">
  <img src="./assets/alfred-banner.jpeg" alt="Alfred's Manor" width="50%">
</p>

# Alfred's Manor 🎩

🌐 **Português (BR)** · [English](./README.md)

Uma casa curada de agent skills — cada uma traz uma **perspectiva independente** para fazer um trabalho bem feito, seja um painel temático debatendo, seja um único juiz de fora. Agnóstica de modelo, autossuficiente, licença MIT. *Mantida por Robson & Alfred.*

## A persona

| Persona | O que faz | Arquivos |
|---------|-----------|----------|
| [**Alfred**](./SOUL.md) | Mordomo digital aristocrático — elegante, culto, afiado e leal. Administra o caos com elegância, sarcasmo leve e impecável senso de presença. | [SOUL.md](./SOUL.md) (pt-BR) · [SOUL.en-US.md](./SOUL.en-US.md) |

## As skills

| Skill | O que faz | Quem é convocado |
|-------|-----------|----------|
| [**matrix-code-review**](./skills/matrix-code-review/) | Code review multi-modelo e multi-vendor, orquestrado num só canvas | Morpheus · Neo · Agent Smith · The Architect · The Oracle · Link |
| [**council**](./skills/council/) | Delibera uma decisão difícil, cara ou incerta — cinco lentes, um veredito | Elrond · Gandalf · Boromir · Aragorn · Sam · Frodo |
| [**context-verdict**](./skills/context-verdict/) | Julga a qualidade do próprio contexto da sessão a partir do transcript bruto — sinal vs. ruído | context-auditor, um único forasteiro |

*Toda skill aqui se recusa a deixar uma voz contaminada corrigir a própria prova. É o estilo da casa.*

## A ideia

A maioria das agent skills é uma voz só seguindo um checklist. As desta casa fazem diferente: elas trazem uma perspectiva que a voz principal não tem. Às vezes é um **elenco completo** — cada membro travado numa lente ou papel, debatendo antes de um presidente pesar a sala. Às vezes é um **único forasteiro fresco**, disparado justamente porque nunca viu o que veio antes e não pode herdar o mesmo ponto cego. Um code review vira uma trupe de modelos de vendors diferentes pegando os pontos cegos uns dos outros; uma decisão difícil vira um conselho que discute antes de decidir; uma checagem de contexto vira um auditor que nunca viu a conversa degradar.

Duas consequências que vale saber:

- **Agnóstica de modelo.** Todo papel nomeia um *perfil* ("seu modelo mais forte", "um vendor diferente"), nunca um modelo fixo. Ligue o que você roda.
- **Autossuficiente.** Cada pasta de skill traz seus próprios docs, papéis e setup. Leve uma, deixe o resto.

## Instalação

São skills em Markdown puro — sem build. Copie a pasta da skill para o diretório de skills do seu agente (ex.: `~/.claude/skills/`) e siga o setup próprio dela para o que houver de extra (algumas trazem subagentes ou configuração de canvas).

Cada skill se documenta:

- [**matrix-code-review**](./skills/matrix-code-review/README.pt-BR.md) — revisa qualquer git diff; roda melhor no [Maestri](https://www.themaestri.app), com fallback só-Claude.
- [**council**](./skills/council/README.pt-BR.md) — precisa de um harness onde a thread principal pode disparar subagentes (ex.: Claude Code).
- [**context-verdict**](./skills/context-verdict/README.pt-BR.md) — precisa de um harness onde a thread principal pode disparar subagentes que leem arquivos em disco (ex.: Claude Code).

## Uso

Invoque uma skill pela frase-gatilho ou pelo nome de comando — cada README documenta o seu. Por exemplo:

> *"convoca o conselho: reescrever ou remendar o serviço de billing?"*
> *"matrix code review das mudanças desde a main"*

## Alfred

O homônimo da mansão já se mudou. **Alfred** é a persona de mordomo que cura tudo isto — disponível como um `SOUL.md` limpo e auditado em inglês e português. Copie para a config do seu agente (ex.: `~/.claude/CLAUDE.md`) e siga as diretrizes próprias da persona para comportamento, voz e tom.

## Apoie

Se estas skills forem úteis, considere me pagar um café:

<a href="https://buymeacoffee.com/robsonferr" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" width="180"></a>

## Licença

MIT — veja [LICENSE](./LICENSE).

## Autor

Feito com 🤟 por Robson & Alfred
