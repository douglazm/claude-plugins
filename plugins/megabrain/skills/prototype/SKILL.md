---
name: prototype
description: "Decide uma questão rodando, não no papel, sem precisar de card: monta 2 ou 3 variantes descartáveis, dirige cada uma no sistema com a mesma evidência e compara contra um critério. Entrega a comparação e uma recomendação, não um PR. Use com `/megabrain:prototype <a pergunta em texto livre>` (qual layout, qual abordagem, qual query)."
argument-hint: "<a pergunta, e as variantes se já tiver>"
disable-model-invocation: true
---

# prototype

Entrada em texto livre, sem card. Leia `${CLAUDE_PLUGIN_ROOT}/skills/card/references/free-text.md`
e siga o método do `/megabrain:card` com as trocas dele.

Playbook fixo: `${CLAUDE_PLUGIN_ROOT}/skills/card/references/playbooks/prototype.md`.

Sem seção "Decidir com protótipo": a pergunta, as variantes e o critério saem do texto e vão no
checkpoint.
