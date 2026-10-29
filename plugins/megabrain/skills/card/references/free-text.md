# Entrada em texto livre

Os comandos de playbook (`/megabrain:bug`, `:feature`, `:prototype`, `:multi-pr`, `:map`) usam
o mesmo método do `/megabrain:card`, sem exigir card. A entrada é o que o dev escreveu: um
sintoma, um pedido, uma thread colada, uma pergunta.

Siga `${CLAUDE_PLUGIN_ROOT}/skills/card/SKILL.md` inteiro (o "Antes de tudo", o §2 em diante e
"O motor"), com estas trocas:

| No card | Em texto livre |
|---|---|
| §1: ler o card no ClickUp | Leia o texto do dev. Se trouxer link ou id de card (`868…`), leia o card como no §1 e siga como `/megabrain:card`. Se citar arquivo, tela, print ou thread, abra. |
| O card é a especificação | O texto é a especificação. Não complete o que ele não diz com suposição: o que falta e muda o resultado vai para `Dúvida:`. |
| Roteador do §3 | O playbook já vem do comando. Se o texto claramente pede outro (pediu `bug` e é pergunta de "por quê"), diga no checkpoint e troque. |
| Checkpoint: `Card: <nome> (<id>)` | `Pedido: <o texto do dev, numa linha>` |
| Branch com o id do card | `<tipo>/<resumo-curto>` (por exemplo `fix/pix-troco-zerado`). Com card, o id entra como no card. |
| `.agent-evidence/<branch>/report.md` | Igual. O título do report é o pedido. |
| Entrega: texto pronto para colar no card | Só se houver card. Sem card, o resumo vai no corpo do PR. |

Todo o resto vale igual: um checkpoint, no máximo uma pergunta, o contrato de autonomia, a
profundidade proporcional, a barra de prova e a entrega por link de criação do PR (nunca criar
o PR).

O dev quer só entender, não mudar? É `/megabrain:why`, `/megabrain:how` ou `/megabrain:teach`.
Diga e siga por lá.
