# Teste de campo — protocolo v1

Protocolo de validacao empirica do ctxos. Unica pergunta em aberto:

> O indice incremental (index → locate → commit) reduz o custo de contexto
> sem reduzir a qualidade das entregas?

Tudo que nao serve pra responder essa pergunta fica fora do escopo da v1.

## Alvo

Projeto real (Ezer), tarefas reais de desenvolvimento — nao sintetico.

## Metricas (v1 — so estas 5)

| # | Metrica | Como medir |
|---|---|---|
| Precisao | `/ctxos:locate` devolveu o no/arquivo certo? | Por tarefa, sim/nao apos verificacao humana. |
| Tempo | Tempo total da tarefa, com ctxos vs estimativa sem ctxos. | Cronometrar do pedido ate o commit. |
| Tokens | Tokens aproximados gastos (saida do locate + arquivos carregados). | Estimativa bytes/4 ou contagem da ferramenta. |
| Drift | Precisou corrigir `.ctxos/` na mao apos locate/commit? | sim/nao — qualquer edicao manual conta. |
| UX | 0–10 ao fim da sessao: "sentiria falta disso amanha?" | Nota livre, 1 por sessao (nao por tarefa). |

Qualquer outra metrica (saltos do funil, raio do grafo, degradacao pos-100-commits,
etc.) fica **fora do escopo da v1** — nao medir agora.

## Criterio de morte

ctxos consumindo >10-15% do custo total de inferencia OU >10-15% do tempo
total da tarefa, em regime continuo, mata a hipotese independente das
outras metricas.

## Formato de registro

Uma linha por tarefa real:

| Tarefa | Locate acertou? | Tempo | Tokens | Drift (correcao manual)? | Obs |
|---|---|---|---|---|---|
| <texto> | sim/nao | <min> | <N> | sim/nao | <nota livre> |

UX (0–10) registrado a parte, 1 por sessao de trabalho, nao por tarefa.

## Definition of Done

v1 termina quando existirem:

- 30 tarefas reais registradas na tabela acima;
- todas as 5 metricas preenchidas;
- conclusao objetiva: **hipotese confirmada** ou **hipotese rejeitada**.

Nenhuma decisao sobre ctxos virar produto, virar kernel, ganhar scheduler,
ou nunca ganhar scheduler, e tomada antes desse ponto.

## Aprendizados do Field Test (parcial)

Fatos observados durante o field test. Sem conclusao arquitetural, sem
virar feature — so registro.

- O locate forneceu contexto suficiente pra execucoes longas.
- O agente consegue decompor tarefas internamente.
- As interrupcoes observadas foram de negocio, nao de codigo.
