---
name: map
description: "Mapeia o fluxo de negócio de uma área dirigindo o sistema de verdade e grava em `.megabrain/flows/`: pré-condições, passos, asserções conferidas no banco, o que é proposital. Sem precisar de card. Use com `/megabrain:map <a área em texto livre>` (fechamento de caixa, pedido de salão, emissão de NFC-e)."
argument-hint: "<a área ou o fluxo a mapear>"
disable-model-invocation: true
---

# map

Entrada em texto livre, sem card. Leia `${CLAUDE_PLUGIN_ROOT}/skills/card/references/free-text.md`
e siga o método do `/megabrain:card` com as trocas dele.

Sem playbook próprio: siga `.megabrain/flows/README.md` ("Como mapear") e o contrato de
`${CLAUDE_PLUGIN_ROOT}/skills/card/references/flow-contract.md`.

No checkpoint, `Vou provar:` são os passos que você vai dirigir e as asserções de banco que vai
conferir. A entrega é o fluxo novo ou corrigido em `.megabrain/flows/` (e a linha na fila do
README), numa branch `docs/<fluxo>`, com o link de criação do PR. Sem mudança de código.

Só quer entender a área sem dirigir nem gravar nada? É `/megabrain:how`.
