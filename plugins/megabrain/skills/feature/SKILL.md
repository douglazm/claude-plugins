---
name: feature
description: "Implementa um comportamento novo ou mudado e prova no sistema rodando, sem precisar de card: deriva o critério de aceite do texto, implementa pelo padrão do módulo, testa e dirige as telas. Entrega a branch e o link de criação do PR. Use com `/megabrain:feature <pedido em texto livre>`."
argument-hint: "<o que muda e para quem>"
disable-model-invocation: true
---

# feature

Entrada em texto livre, sem card. Leia `${CLAUDE_PLUGIN_ROOT}/skills/card/references/free-text.md`
e siga o método do `/megabrain:card` com as trocas dele.

Playbook fixo: `${CLAUDE_PLUGIN_ROOT}/skills/card/references/playbooks/feature.md`.

Sem análise técnica: o critério de aceite é o que você escreve em `Vou provar:` no checkpoint,
derivado do texto. Cada escolha pequena vai para `## Decisões` do report.
