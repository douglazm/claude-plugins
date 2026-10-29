---
name: why
description: "Responde 'por que isso é assim?' com fonte: motivação e intenção por trás de um trecho, regra, número ou decisão. Ancora no código, investiga em paralelo o histórico (git e PRs, cards e comentários do ClickUp, docs, chat, observabilidade, os fluxos da `.megabrain/`) e devolve uma resposta que separa fato citado de inferência e do que ninguém sabe. Não muda código. Use com `/megabrain:why <pergunta em texto livre>`. Para 'como funciona', use `/megabrain:how`."
argument-hint: "<pergunta em texto livre: por que X é assim? por que escolhemos Y?>"
disable-model-invocation: true
---

# Why

O código diz o que acontece. Quase nunca diz por que alguém escreveu assim. Esta skill acha o
porquê no histórico e diz, com a mesma honestidade, o que o histórico não sustenta. Não muda
código, não comenta no card, não abre PR.

Não precisa de card. A entrada é a pergunta do dev, em texto livre. Se vier um link de card,
ele é uma fonte a mais, não a especificação.

## Postura

Investigador cuidadoso: preciso e honesto sobre o que sabe e o que deduz. Leia
`${CLAUDE_PLUGIN_ROOT}/skills/why/references/epistemics.md` antes de responder. As faixas de confiança dele valem para toda
frase da resposta.

Hipótese embutida na pergunta ("é por performance, né?") é uma candidata, não a conclusão.
Confira como qualquer outra.

## 1. Alvo e pergunta

O **alvo** é um trecho, uma regra, uma tela, um número, uma decisão. A **pergunta** é o tipo
de porquê: motivo de design, troca feita, caso de borda que forçou, restrição externa, código
morto, ou a história inteira de uma área.

Pergunta vaga ("por que é assim?" sem referente): deduza pelo contexto da conversa (arquivos
abertos, o que acabou de ser discutido, o diff da branch), diga em uma linha o que entendeu e
siga. O dev corrige se for outra coisa.

## 2. Âncora no código

Monte antes de qualquer subagente, na thread principal:

- arquivos e linhas do alvo, símbolos principais (`grep` pela mensagem, pelo campo, pela rota);
- os commits que tocaram aquelas linhas e os PRs deles:
  ```bash
  git log -L <início>,<fim>:<arquivo> --oneline     # a história daquelas linhas
  git log -S '<trecho literal>' --oneline           # quando o trecho apareceu ou sumiu
  git blame -w -C -L <início>,<fim> <arquivo>
  git log --follow --oneline -20 -- <arquivo>       # PR no assunto: (#1234)
  gh pr view <n> --json title,body,author,mergedAt,comments,reviews
  ```
- ids de card citados em commit, branch ou corpo de PR (`868…` no ClickUp);
- o que a `.megabrain/` do projeto já registra sobre o alvo: `flows/`, `features/`,
  `blind-spots.md`, `domain-pitfalls.md`. Um fluxo diz o que foi visto dirigindo e se a
  regra é proposital ("não é regressão").

A âncora vai inteira para cada investigador.

## 3. Investigadores em paralelo

Liste os MCPs disponíveis na sessão e ligue cada um a uma categoria de evidência:

| Categoria | Exemplos | Melhor para |
|---|---|---|
| Controle de versão | git, `gh` (sempre disponível) | o motivo registrado na hora do PR e da revisão |
| Tarefas | ClickUp (tarefas e comentários), Jira, Linear | o pedido de produto ou do cliente por trás |
| Documentos | ClickUp Docs, Notion, Google Drive | o desenho escrito antes de virar código |
| Chat | ClickUp Chat, Slack | a conversa que nunca virou doc |
| Observabilidade | New Relic, Datadog, Grafana | a realidade de produção que motivou retry, timeout, limite |
| Erros | Sentry, New Relic Errors | a exceção que motivou a guarda |

Um MCP pode servir a mais de uma categoria (o ClickUp tem tarefas, docs e chat): um
investigador por **categoria**, não por MCP.

Dispare todos numa mensagem só, com a ferramenta de subagente (`general-purpose`: os
investigadores precisam dos MCPs). O prompt de cada um é `${CLAUDE_PLUGIN_ROOT}/skills/why/references/investigator.md`
preenchido, com a âncora e a pergunta do dev.

**Pular uma categoria** só com motivo escrito, que vai para "Fontes consultadas":
- não há MCP para ela nesta sessão (é lacuna, não escolha);
- é comprovadamente irrelevante (script de build não tem erro de produção).

Alvo trivial, de um commit só, cujo PR já responde por inteiro: responda sem subagentes e diga
isso. É raro.

## 4. Síntese

Na thread principal, com os achados de todos os investigadores, inclusive os vazios:

1. junte o que vários citam (o mesmo PR, o mesmo card) numa referência só;
2. mostre as contradições lado a lado, sem escolher a que conta a história mais limpa;
3. ponha cada afirmação na sua faixa (`${CLAUDE_PLUGIN_ROOT}/skills/why/references/epistemics.md`);
4. confira a citação que parecer duvidosa abrindo a fonte. Não propague erro de investigador.

## 5. Resposta

No formato de `${CLAUDE_PLUGIN_ROOT}/skills/why/references/answer.md`. A primeira frase responde a pergunta. Sem fonte
nenhuma também é resposta: "o código faz X desde `<commit>`, sem PR nem card que explique".

Se a pergunta vem antes de mudar o código, feche com o bloco **Preservar / Mudar / Evitar /
Risco**, que é o que um `/megabrain:feature` ou `/megabrain:bug` vai precisar.

## Erros comuns

- **Viés do recente.** O último commit raramente é o motivo. O formato de hoje costuma ser o
  acúmulo de decisões antigas. Volte na história.
- **Código como prova da própria intenção.** "Usa um `Map` porque precisava de busca rápida"
  é leitura sua, não fonte.
- **Racionalizar.** Não invente um motivo limpo para história bagunçada. Copiar e colar
  também explica padrão.
