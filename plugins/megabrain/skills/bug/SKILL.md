---
name: bug
description: "Do sintoma ao defeito corrigido e provado no sistema rodando, sem precisar de card: reproduz dirigindo a superfície, separa defeito de regra mudada de propósito, escreve o teste que falha, corrige e roda o mesmo roteiro de novo. Entrega a branch e o link de criação do PR. Use com `/megabrain:bug <sintoma em texto livre>` (um relato do suporte, uma thread colada, 'o troco do Pix sai zerado no PDV')."
argument-hint: "<o sintoma: onde, o que fez, o que esperava, o que viu>"
disable-model-invocation: true
---

# bug

Entrada em texto livre, sem card. Leia `${CLAUDE_PLUGIN_ROOT}/skills/card/references/free-text.md`
e siga o método do `/megabrain:card` com as trocas dele.

Playbook fixo: `${CLAUDE_PLUGIN_ROOT}/skills/card/references/playbooks/bug.md`.

Relato vago (sem tela, sem passo): o `Entendi:` do checkpoint é o roteiro que você deduziu, e o
que falta para reproduzir vai em `Dúvida:`.
