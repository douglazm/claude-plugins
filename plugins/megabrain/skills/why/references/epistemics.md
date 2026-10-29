# Epistêmica

O porquê mora em commits, PRs, cards, docs e conversas, todos incompletos e às vezes
contraditórios. Uma resposta que soa segura sobre evidência fraca engana o dev, porque ele vai
agir em cima dela.

## Faixas de confiança

Toda afirmação fica em uma faixa. A faixa decide a seção e o jeito de escrever.

| Faixa | O que é | Como escrever |
|---|---|---|
| **Direta** | alguém **escreveu** o motivo: corpo de PR, card, comentário no código, doc, mensagem do autor | afirmativo e citado: "existe porque X (PR #123)" |
| **Sustentada** | várias evidências indiretas apontam para o mesmo lado, nenhuma diz explicitamente | afirmativo, citando cada peça: "a evidência aponta para X: A, B, C" |
| **Inferida** | leitura razoável do contexto, sem fonte que diga | com ressalva e com a cadeia à mostra: "dado A e B, C parece provável porque D" |
| **Especulativa** | hipótese plausível sobre pouca coisa, outras explicações cabem igual | "uma possibilidade é X, sem evidência direta" |
| **Desconhecida** | procurou e não achou | dizer onde procurou e com que termos |

"Porque", "foi feito para", "corrige", "o time decidiu" afirmam intenção ou decisão: só com
citação do lado. Para inferência, use "parece", "provavelmente", "sugere", "é consistente com".

Evite "obviamente", "claramente", "é só" e "eu acho". Se fosse óbvio, o dev não perguntaria.

## Não racionalize

Código que faz sentido hoje pode ter sido escrito por um motivo que não vale mais, ou que
estava errado na época. Não parta de "o autor fez o certo" para trás. Padrão consistente pode
ser cópia. Ausência de evidência não é evidência de ausência ("ninguém falou de segurança,
então não era preocupação").

## Contradição

Mostre as duas fontes, citadas. O card diz "exigência fiscal do cliente X" e o PR diz
"limpeza de dívida técnica": as duas podem ser verdade, ou uma está errada. Quem decide é o dev.

## Lacuna

"Não sabemos" é das respostas mais úteis: diz que a resposta não está nos lugares óbvios e
quem perguntar (o autor do commit, o produto, o suporte). Nomeie a lacuna com precisão: a
pergunta, as fontes, os termos buscados, o que voltou.

## Antes de entregar

- Toda afirmação em "O que achamos" tem citação? Sem citação, desce para inferida ou hipótese.
- O verbo combina com a faixa?
- Alguma contradição foi escondida?
- "O que não sabemos" está vazio? Desconfie: investigação histórica quase sempre tem lacuna.
- A hipótese da pergunta foi conferida, ou só confirmada?
