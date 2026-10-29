---
name: how
description: "Responde 'como isso funciona?' com um modelo mental de quem vai mexer na área: o fluxo em tempo de execução de ponta a ponta (app, API, fila, banco), onde cada coisa mora e o que é fácil errar. Também responde 'onde isso deveria ficar', 'qual módulo é dono disso', 'é a camada certa'. Explora em paralelo quando a área cruza apps ou módulos. Não muda código. Use com `/megabrain:how <pergunta em texto livre>`. Para 'por que é assim', use `/megabrain:why`."
argument-hint: "<pergunta em texto livre: como funciona X? onde Y deveria ficar?>"
disable-model-invocation: true
---

# How

Explica uma área no nível de um sênior chegando nela: o bastante para montar um modelo mental
e começar a mexer com segurança, sem virar código-fonte comentado. Não muda código.

Não precisa de card. A entrada é a pergunta do dev, em texto livre. Escopo ambíguo: diga em uma
linha o que entendeu e siga. O dev corrige.

## 1. Ponto de partida

Leia o que a `.megabrain/` do projeto já tem sobre a área: o fluxo em `flows/`, as telas em
`features/`, os mapas em `maps/`. São atalhos, nunca verdade: o código manda, e a divergência
entre os dois é um achado. `blast-radius --files <arquivo>` mostra o que um arquivo alcança.

## 2. Tamanho

- **Simples** (um módulo, um utilitário, "como a função X funciona"): sem subagentes. Leia e
  explique na thread principal. Vá ao passo 4.
- **Complexo** (cruza apps, módulos ou serviços; fila, consumer, assinatura em tempo real; visão
  geral de uma área): passo 3.

Na dúvida, simples.

## 3. Exploradores em paralelo (só complexo)

Corte a pergunta em 2 a 4 ângulos, cada um uma fatia distinta (por exemplo: a tela e o estado
no app; o resolver, o use case e o domínio na API; a fila, o consumer e o banco). Dispare todos
numa mensagem só, com subagentes `Explore`, cada um com `${CLAUDE_PLUGIN_ROOT}/skills/how/references/explorer.md` preenchido.

Quando voltarem, junte na thread principal: funda o que se repete, resolva contradição lendo o
código, feche as lacunas que eles apontaram.

## 4. Resposta

No formato de `${CLAUDE_PLUGIN_ROOT}/skills/how/references/explanation.md`. Cite `arquivo:linha` para o dev saber onde olhar.
Diga o que não conseguiu rastrear.

Achou uma pré-condição ou armadilha que nenhum fluxo da `.megabrain/` registra? Ofereça
acrescentar no fluxo, uma linha. Não escreva sem o dev pedir.
