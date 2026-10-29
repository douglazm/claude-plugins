---
name: multi-pr
description: "Corta uma mudança que mexe em contrato (API, tipos, SDK) e em apps numa pilha de PRs pequenos, na ordem de dependência, cada um provado sozinho, sem precisar de card. Entrega os links de criação, um por PR. Use com `/megabrain:multi-pr <a mudança em texto livre>`."
argument-hint: "<a mudança e as áreas que ela toca>"
disable-model-invocation: true
---

# multi-pr

Entrada em texto livre, sem card. Leia `${CLAUDE_PLUGIN_ROOT}/skills/card/references/free-text.md`
e siga o método do `/megabrain:card` com as trocas dele.

Playbook fixo: `${CLAUDE_PLUGIN_ROOT}/skills/card/references/playbooks/multi-pr.md`.

Sem TL;DR de análise técnica: o corte (quantos PRs, em que ordem) sai do `blast-radius` nos
arquivos que você vai tocar e vai no checkpoint.
