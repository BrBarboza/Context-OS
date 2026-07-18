---
description: API de sessao pro esforco de raciocinio ANTES de implementar — fast (sem extended thinking, single-pass), normal (comportamento atual) ou deep (extended thinking / ultrathink, considera trade-offs e arquitetura). Nunca muda mode nem output.
argument-hint: "fast|normal|deep"
disable-model-invocation: true
---

`$ARGUMENTS` nem `fast` nem `normal` nem `deep`: nao adivinhar, mostrar uso — `/ctxos:think fast|normal|deep`.

## fast

Sem extended thinking. Implementacao single-pass: pula listagem de alternativas e discussao de trade-off. Ideal pra mudanca local, trivial, baixo risco.

## normal

Comportamento atual — sem instrucao extra de raciocinio.

## deep

Aciona extended thinking (equivalente a "ultrathink") antes do passo de implementacao. Considerar trade-offs, impacto arquitetural, alternativas rejeitadas — registrar rejeitadas relevantes como Decision Record no commit seguinte.

## Confirmacao

Ativar em 1 linha: "Think: Fast" / "Think: Normal" / "Think: Deep".

## Guardrail

Escopo estrito: `think` so encosta no passo "implementar" de `/ctxos:mode` (manual ou autonomous). Nunca muda `/ctxos:locate` (funil, fingerprint, staleness-repair) nem `/ctxos:commit` (write-through, split, deriva semantica) — mecanica estrutural desses dois planos e fixa, imune a nivel de pensamento. Nunca introduz pausa nova — pausa e papel exclusivo de `/ctxos:mode`. Estado vive na conversa, nunca em arquivo — sessao nova reseta pra `normal`.

Uma vez ativado, o nivel vale pra TODA implementacao seguinte nesta conversa — comando ou pedido em linguagem natural, dentro de `/ctxos:mode manual` ou `autonomous` — sem o usuario precisar chamar `/ctxos:think` de novo a cada pedido. So muda quando `/ctxos:think` for chamado com outro valor, ou a conversa reiniciar (volta pra `normal`).
