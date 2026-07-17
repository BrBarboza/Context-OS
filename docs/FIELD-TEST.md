# Teste de campo — protocolo (nao executado)

Este documento e o protocolo de validacao empirica do ctxos contra uso real.
Nao foi rodado ainda — define o que rodar, como medir, e o criterio de
morte da arquitetura.

## Alvo

SaaS real, ~50 arquivos, com historico git existente (nao criado do zero
pro teste — precisa ter deriva organica acumulada).

## Baseline

Fluxo antigo (sem ctxos): contexto acumulado manualmente por sessao de
trabalho, ~150k tokens carregados em media pra sessoes de tamanho medio.
Toda metrica de custo abaixo compara contra esse numero.

## Metricas

| # | Metrica | Meta | Como medir |
|---|---|---|---|
| M1 | No certo em ≤3 saltos | ≥80% dos pedidos | Contar saltos do funil (ROOT → index → arquivo) por pedido real, log manual. |
| M2 | Custo por locate | ≤2k tokens | Somar tokens da saida de locate + arquivos que ela manda carregar. |
| M3 | Falso-stale apos rebase proposital | =0 | Fazer rebase+squash deliberado, rodar locate/commit, contar quantos nos foram marcados stale sem terem mudado de conteudo. |
| M4 | Drift real detectado | ≥1 em 2 semanas | Contar entradas "drift: <no> reproposito" no output/Decision Records ao longo do periodo. |
| M5 | Edicoes manuais nos planos | 0 | Diff de `.ctxos/index`, `graph.md`, `memory`, `ledger` fora de index/locate/commit. Repete o criterio de manutencao-zero da rubrica. |
| M6 | Precisao do no devolvido | ≥85% | Por pedido real, validacao humana: "o no que locate devolveu era o certo?" sim/nao. |
| M7 | Raio do grafo | dependencias previstas vs alteradas vs faltantes | Por pedido, comparar arestas que locate expandiu contra o que realmente mudou no commit. Faltante = furo no grafo, contar. |
| M8 | Sessao fria (teste acido vivo) | pass/fail | Conversa nova, ler SO `ledger/current.md` + `index/ROOT.md`, executar 1 item do BACKLOG corretamente sem contexto adicional. |
| M9 | Degradacao apos uso continuo | ≥90% corretos | Apos ~100 commits, amostrar 10 indexes ao acaso, validar cada um contra o codigo real que descreve. |

## Criterio de morte

ctxos consumindo >10-15% do custo total de inferencia do fluxo de trabalho
em regime continuo (index+locate+commit somados vs todo o resto da sessao)
= a infraestrutura de contexto compete com o trabalho que ela deveria
acelerar. Nesse caso a arquitetura falhou, independente de qualquer outra
metrica acima estar verde.

## Formato de registro

Uma tabela por pedido real, preenchida durante o teste:

| Pedido | Saltos | No devolvido | Correto? | Tokens (locate) | Observacao |
|---|---|---|---|---|---|
| <texto do pedido> | <N> | <caminho do no> | sim/nao | <N> | <nota livre, ex: furo no grafo, drift pego, etc> |

Uma linha por pedido, cronologica. M1/M2/M6 saem direto de agregacoes desta
tabela; M3/M4/M5/M7/M8/M9 sao eventos discretos registrados a parte quando
ocorrem (rebase proposital, drift, edicao manual achada, sessao fria,
amostragem pos-100-commits).
