---
description: API de sessao pro nivel de detalhe da resposta final — compact (checklist enxuto + arquivos tocados) ou verbose (comportamento atual, explica decisoes/nos/memoria/raio). Nunca infla o funil de locate nem muda o que commit escreve em .ctxos/.
argument-hint: "compact|verbose"
disable-model-invocation: true
---

`$ARGUMENTS` nem `compact` nem `verbose`: nao adivinhar, mostrar uso — `/ctxos:output compact|verbose`.

## compact

Resumo final so checklist enxuto — etapas concluidas + lista de arquivos tocados, sem explicar decisao/no/raio/memoria.

Exemplo:

```
✔ Localizado
✔ Implementado
✔ Commitado

Arquivos:
- src/navbar.tsx
- src/sidebar.tsx
```

## verbose

Comportamento atual do Context OS: explica decisoes, nos, memoria, raio, eventos intermediarios relevantes (ex: "Locate concluido", "N arquivos carregados").

## Header de sessao

Toda resposta que envolver trabalho de `/ctxos:mode` ativo abre com:

```
CTXOS
Mode: <Manual|Autonomous>
Think: <Fast|Normal|Deep>
Output: <Compact|Verbose>
────────────────────────
```

Mostrar sempre, independente do valor de `output` — o header e status, nao conteudo. So o corpo da resposta muda de formato entre compact/verbose.

## Confirmacao

Ativar em 1 linha: "Output: Compact" ou "Output: Verbose".

## Guardrail

Escopo estrito: `output` so formata a saida voltada ao usuario nos passos de `/ctxos:mode` (resumo final, mensagens intermediarias). Nunca infla a saida de `/ctxos:locate` — teto de ≤12 linhas e orcamento de token fixo, nao preferencia de verbosidade. Nunca muda o que `/ctxos:commit` escreve em `.ctxos/`. Estado vive na conversa, nunca em arquivo — sessao nova reseta pra `verbose`.

Uma vez ativado, o nivel vale pra TODA resposta operacional seguinte nesta conversa — sem o usuario precisar chamar `/ctxos:output` de novo a cada pedido. So muda quando `/ctxos:output` for chamado com outro valor, ou a conversa reiniciar (volta pra `verbose`). Header aparece em toda resposta operacional independente do nivel — so o corpo alterna compact/verbose.
