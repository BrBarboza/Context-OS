---
description: Diagnostico read-only dos 4 planos + runtime. So roda sob pedido explicito do usuario — nunca sugerir execucao periodica (violaria o Axioma 0). Nunca repara, so reporta.
argument-hint: "[--report]"
disable-model-invocation: true
---

Sem `.ctxos/ledger/current.md`: pare, avise "rode /ctxos:index primeiro" — doctor nao bootstrapa.

Contrato: doctor NUNCA escreve em `.ctxos/`. Nenhum passo abaixo repara nada — reparo e papel de `locate`/`commit`. Doctor so le e reporta.

1. **INDEX** — pra cada `.ctxos/index/<caminho>.md`: recomputar fingerprint (mesma formula do index — sha256 de path+conteudo dos arquivos-chave, ordem alfabetica) e comparar com `verified:` guardado. Divergiu → listar como stale.
2. **GRAPH** — pra cada aresta `A -> B (motivo)` em `.ctxos/graph.md`: `A` e `B` tem que ter index correspondente em `.ctxos/index/`. Origem ou destino sem index → aresta orfa.
3. **MEMORY** — pra cada `.ctxos/memory/<no>.md`: `<no>` tem que ter index correspondente → sem index, arquivo orfao. No com >10 Decision Records atomicos (fora do bloco "estado atual") e sem destilacao → flag.
4. **LEDGER** — `.ctxos/ledger/current.md` acima de ~1 pagina (~40 linhas) → flag. `verified:` do ledger comparado com fingerprint recomputado de `.ctxos/index/ROOT.md` → divergiu, flag.
5. **RUNTIME** — `.ctxos/runtime/judgments.log`: >200 linhas sem entrada correspondente arquivada em `.ctxos/runtime/archive/` → flag.

Saida padrao (sem `--report`): 1 linha por problema encontrado, formato `<plano>: <problema> — <no/arquivo>`. Zero problemas → so `Planos saudaveis.`

Flag `--report`: tabela completa, 1 linha por plano verificado (index, graph, memory, ledger, runtime), status `ok` ou `<N> problema(s)` mesmo quando ok — nunca omitir plano saudavel da tabela.

Nunca recomendar agendamento, cron, hook automatico ou qualquer execucao recorrente de doctor em output, doc ou commit message — diagnostico e sempre sob demanda explicita do usuario.
