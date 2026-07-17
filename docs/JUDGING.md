# Rubrica de avaliacao do ctxos

Peso ponderado. Meta: nota ponderada ≥8.8, com manutencao-zero = 10 obrigatorio
(nota abaixo de 10 nesse criterio invalida a nota final, independente do resto —
e o Axioma 0 da spec).

| Criterio | Peso | O que mede | Como medir |
|---|---|---|---|
| Manutencao-zero | 2x | Edicoes manuais nos 4 planos a cada 10 itens de trabalho. Meta: 0. | Contar diffs em `.ctxos/index`, `.ctxos/graph.md`, `.ctxos/memory`, `.ctxos/ledger/current.md` que NAO vieram de `/ctxos:index` ou `/ctxos:commit`. Qualquer edicao manual = furo. |
| Tokens | 2x | Custo de carregar contexto. `locate` ≤12 linhas de saida; sessao nova produtiva com ≤2k tokens de carga (ROOT + ledger/current + 1 index). | Contar linhas da saida de locate; somar tamanho de `index/ROOT.md` + `ledger/current.md` + index do no mais provavel. |
| Staleness-honesta | 1x | Um no suspeito (fingerprint divergiu) nunca finge frescor — sempre repara integral antes de responder, ou avisa que nao reparou. Sobrevive a rebase/squash/no-git (V1). | Reescrever arquivo de um no por fora do fluxo ctxos, rodar locate, checar se o fingerprint acusou a diferenca e o index foi reparado por INTEIRO antes da resposta — mesmo apos squash do commit que originou o `verified:` anterior. |
| Resultado | 1x | O funil realmente acha o no certo e o raio certo pro pedido; auto-invocacao de locate acerta o gatilho (V4). | Rodar locate com pedidos reais, checar arquivo(s) certo(s) na lista minima. Testar 1 pedido com alvo explicito (nao deve auto-invocar) e 1 conceitual (deve). |
| Praticidade | 1x | Uso manual sem fricção — 3 comandos bastam, sem setup extra por sessao, funciona sem git. | Rodar os 3 comandos em sequencia num projeto real do zero, com e sem `.git`. |
| Portabilidade | 1x | Teste acido: apagar o plugin, dar `.ctxos/` pra um modelo cru — ele continua o projeto so lendo ledger/current+ROOT? | Ver "Teste acido" no README — resposta tem que ser sim. |
| Alucinacao | 1x | Planos nunca afirmam algo que o codigo atual contradiz; deriva semantica (`proposito:` desatualizado) e corrigida no commit, nao acumula (V8). | Comparar conteudo de um index contra o arquivo real que ele descreve, pos-staleness-check. Checar se um `proposito:` que ficou obsoleto foi reescrito no commit seguinte que tocou o no. |

## Notas de aplicacao

- **Manutencao-zero** e o unico criterio com veto: uma edicao manual fora de
  index/locate/commit em qualquer um dos 4 planos zera esse criterio pro ciclo
  avaliado, mesmo que o resto va bem.
- **Tokens**: o teto de 12 linhas em locate e literal — se a saida precisar de
  mais pra ficar completa, o problema e o raio (amplo demais), nao o teto.
- **Staleness-honesta** e o criterio mais facil de furar silenciosamente: um
  index desatualizado que "parece" plausivel e pior que um que admite nao saber.
- **Teto soft (V6)**: commit acima de ~20 linhas alteradas so passa se vier
  com a justificativa de 1 linha no output. Sem justificativa = furo, mesmo
  que o conteudo escrito esteja correto.
