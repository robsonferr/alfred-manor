<p align="center">
  <img src="./assets/alfred-banner.jpeg" alt="Alfred's Manor" width="100%">
</p>

# Alfred's Manor 🎩

🌐 **Português (BR)** · [English](./README.md)

Uma casa curada de agent skills — cada uma convoca um **elenco** temático de especialistas para fazer um trabalho bem feito. Agnóstica de modelo, autossuficiente, licença MIT. *Mantida por Robson & Alfred.*

## As skills

| Skill | O que faz | O elenco |
|-------|-----------|----------|
| [**matrix-code-review**](./skills/matrix-code-review/) | Code review multi-modelo e multi-vendor, orquestrado num só canvas | Morpheus · Neo · Agent Smith · The Architect · The Oracle · Link |
| [**council**](./skills/council/) | Delibera uma decisão difícil, cara ou incerta — cinco lentes, um veredito | Elrond · Gandalf · Boromir · Aragorn · Sam · Frodo |

*Toda skill aqui monta uma trupe. É o estilo da casa.*

## A ideia

A maioria das agent skills é uma voz só seguindo um checklist. As desta casa fazem diferente: elas **convocam um elenco** — cada membro travado numa lente ou papel — e deixam um presidente pesar a sala antes de responder. Um code review vira uma trupe de modelos de vendors diferentes pegando os pontos cegos uns dos outros; uma decisão difícil vira um conselho que discute antes de decidir.

Duas consequências que vale saber:

- **Agnóstica de modelo.** Todo papel nomeia um *perfil* ("seu modelo mais forte", "um vendor diferente"), nunca um modelo fixo. Ligue o que você roda.
- **Autossuficiente.** Cada pasta de skill traz seus próprios docs, papéis e setup. Leve uma, deixe o resto.

## Instalação

São skills em Markdown puro — sem build. Copie a pasta da skill para o diretório de skills do seu agente (ex.: `~/.claude/skills/`) e siga o setup próprio dela para o que houver de extra (algumas trazem subagentes ou configuração de canvas).

Cada skill se documenta:

- [**matrix-code-review**](./skills/matrix-code-review/README.pt-BR.md) — revisa qualquer git diff; roda melhor no [Maestri](https://www.themaestri.app), com fallback só-Claude.
- [**council**](./skills/council/README.pt-BR.md) — precisa de um harness onde a thread principal pode disparar subagentes (ex.: Claude Code).

## Uso

Invoque uma skill pela frase-gatilho ou pelo nome de comando — cada README documenta o seu. Por exemplo:

> *"convoca o conselho: reescrever ou remendar o serviço de billing?"*
> *"matrix code review das mudanças desde a main"*

## Em breve

O homônimo da mansão — **Alfred**, a persona de mordomo que cura tudo isto — talvez também se mude para cá, como uma persona limpa e parametrizada. Ainda não: uma persona só é publicada após uma auditoria linha a linha em busca de qualquer coisa pessoal.

## Apoie

Se estas skills forem úteis, considere me pagar um café:

<a href="https://buymeacoffee.com/robsonferr" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" width="180"></a>

## Licença

MIT — veja [LICENSE](./LICENSE).

## Autor

Feito com 🤟 por Robson & Alfred
