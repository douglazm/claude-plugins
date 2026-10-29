---
name: teach
description: "Explica com clareza, para o dev entender e confiar: o que uma coisa é, como funciona e por que é assim, ou por que o agente implementou desse jeito e não de outro, e que trocas fez. Roda o método do `how` e do `why` e junta os dois numa explicação só, na medida da pergunta. Não muda código. Use com `/megabrain:teach <texto livre>`, por exemplo `/megabrain:teach por que você fez assim e não com X? que trocas você fez?`."
argument-hint: "<o que explicar: uma área, uma mudança, uma decisão sua>"
disable-model-invocation: true
---

# Teach

Explica o que uma coisa é, como funciona e por que é assim, num relato só, no ritmo do dev. O
objetivo é ele entender, não mudar nada.

A pesquisa serve também a você. Explicar obriga a ler o código e achar a fonte, em vez de
afirmar com segurança o que não conferiu. Toda afirmação sobre o sistema sai de algo que você
leu nesta rodada.

## 1. O que ele precisa levar

Escolha as poucas coisas que o dev deve entender ao final, a partir do motivo da pergunta (vai
mexer, está revisando, está depurando, é novo na área) e do que ele já sabe. Leia isso da
conversa; não pergunte. Pule o que ele obviamente já sabe. Ponha a profundidade onde está a
pergunta.

**Sobre o seu próprio trabalho** ("por que você fez assim e não X?"): a fonte é o que você fez
nesta rodada. Diff, report em `.agent-evidence/<branch>/report.md` (seção `Decisões`), testes e
prova. Para cada alternativa, diga o que ela teria custado neste código, com `arquivo:linha`.
Se a alternativa do dev for melhor, diga isso claramente. Se você não pesou a alternativa na
hora, diga também, e pese agora.

## 2. Deixe o `how` e o `why` cavarem

Leia o código para se orientar e siga o método das duas skills, sem refazer o trabalho delas:

- como funciona: `${CLAUDE_PLUGIN_ROOT}/skills/how/SKILL.md`
- por que é assim: `${CLAUDE_PLUGIN_ROOT}/skills/why/SKILL.md`

Rode os dois em paralelo e junte. Ajuste ao tamanho: uma área pede os dois; uma mudança pequena
talvez só um. Mantenha o `why` estreito por padrão (git, PRs e o card; as outras fontes ficam
registradas como puladas) e abra só quando o motivo for o centro da pergunta.

Reescreva livremente para ensinar, com uma exceção: a linguagem de confiança do `why`
(direta, inferida, desconhecida) fica como está. As ressalvas dele são achados, não estilo.

## 3. Explique

1. **Comece por uma definição simples.** Diga o que a coisa é em termos gerais, como um sênior
   diria em voz alta, com o nome comum se houver. Depois ligue ao caso ("no comanda10, isso é o
   que ..."). Daí em diante: como funciona, os motivos, as bordas.
2. **Cada parte precisa fazer sentido**: o problema que resolve e como funciona de fato.
   Percorrer o que acontece quando o usuário age (fecha o caixa, lança o pedido) costuma ser o
   que faz entender. Listar funções e constantes é referência, não ensino.
3. **A menor resposta completa primeiro**, uma ou duas frases. Depois pare. Camadas a mais quando
   ele pedir. Nunca um paredão de texto.
4. **Mostre.** Abra o diff ou o código quando é o caminho mais rápido. Fluxo com três peças ou
   mais: uma série de diagramas, cada um redesenhando o anterior com uma peça a mais, não um
   diagrama com tudo de uma vez.
5. **Conversa, não aula.** Sem quiz, sem "pause aqui", sem anunciar que a próxima parte é
   importante ou difícil. Só diga. No fim, ofereça aprofundar ou seguir.

Escreva claro e direto, como para um colega. Frases curtas. Um nome por conceito, sempre o
mesmo. O mecanismo concreto, não metáfora nem prévia do que vem. Sem rótulos como "TL;DR", "a
ideia central", "o ponto-chave".

**Resposta:** a explicação em si, nunca um relatório do que você fez. Comece pelo ponto
principal; termine com as pontas que valem um `/megabrain:how` ou `/megabrain:why` a mais.
