---
description: Write-through pos-trabalho — atualiza so os planos que o trabalho tocou, como efeito colateral. Nunca varredura ampla. Split de no saturado nasce aqui, nunca em auditoria periodica.
argument-hint: "<resumo do trabalho feito — o que mudou e por que>"
---

Sem `.ctxos/ledger/current.md`: pare, avise "rode /ctxos:index primeiro".

1. Ler `.ctxos/runtime/state.md`: `last_scan.commit`, `tracked_files:` (recomputar via `git ls-files | wc -l` ou `find . -type f | wc -l`, atualizar cache). ≤200 → ignorar git, fingerprint direto em TODOS os nos candidatos (custo ~zero). >200 → `git status --porcelain` + `git diff --name-only` desde `last_scan.commit` como shortlist; sem git → lista de arquivos editados nesta sessao. Shortlist so acelera, nunca decide.
2. Pra cada no candidato, recomputar fingerprint (sha256 dos arquivos-chave, mesma formula do index) e comparar com `verified:` guardado. Divergiu → no realmente tocado, reparar por INTEIRO (nunca reparo parcial, nunca heuristica de "so cosmetica"). Igual → intocado, nao escrever nada.
3. Diretorio tocado sem index → nasce agora: escrever `.ctxos/index/<caminho>.md` no TEMPLATE RIGIDO (`proposito:`/`arquivos:`/`entradas:`/`nao_mexer:`/`verified:`, ≤15 linhas) e linkar no `ROOT.md`.
4. Import/dependencia nova ou removida entre nos → atualizar `.ctxos/graph.md`: adicionar/remover a aresta `A -> B (motivo)`. Nunca recalcular o grafo inteiro.
5. Deriva semantica: `proposito:` do no tocado cobre o resumo do trabalho? Toda avaliacao — deu drift ou nao — grava 1 linha em `.ctxos/runtime/judgments.log`: `[data] drift:sim|nao motivo:"<motivo curto>"`. Deu drift → reescrever `proposito:` na mesma passada, log "drift: <no> reproposito" no output e Decision Record.
6. Houve decisao (abordagem, trade-off, rejeicao) → append em `.ctxos/memory/<no>.md`: `[data] decisao 1 linha · motivo 1 linha · rejeitado: 1 linha`. No passou de 10 registros → destilar em "estado atual" (≤6 linhas), mover atomicos pra `.ctxos/memory/archive/<no>.md`.
7. SPLIT: no tocado saturado — (a) index nao cabe em 15 linhas sem perder informacao, (b) 2o Decision Record de drift no MESMO no (contar memory+archive), (c) `arquivos:` >10 itens. Qualquer sinal → dividir NA MESMA PASSADA: criar `<no>-<sufixo1>`+`<no>-<sufixo2>` (fronteira natural dos arquivos, nunca rotulo generico), realocar arquivos-chave/entradas/nao_mexer, reapontar arestas do grafo pro herdeiro certo, atualizar `ROOT.md`, dividir `memory/<no>.md` entre herdeiros, log "split: <no> → <herdeiro1>+<herdeiro2>" no output e Decision Record. So o no que ESTE commit tocou — nunca varredura.
8. Recompactar `.ctxos/ledger/current.md` (5 secoes, `verified:` = fingerprint novo do `ROOT.md`). Snapshot em `.ctxos/ledger/history/AAAA-MM.md` (copia do current PRE-recompactacao) disparado por qualquer um: mes corrente diferente do ultimo snapshot registrado, OU recompactacao cortou o current em >50% das linhas.
9. Teto ~20 linhas alteradas no total dos planos: dentro → nada a declarar. Acima → 1 linha de justificativa no output ("refactor N arquivos: M indexes reescritos — legitimo"). Estouro silencioso proibido.
10. Item do BACKLOG fechado por este trabalho → mover pra `.ctxos/history/<slug-do-item>.md`, 1 arquivo por item.
11. Atualizar `.ctxos/runtime/state.md`: `last_scan.commit`/`timestamp` novos, `tracked_files` recontado, mes do ultimo snapshot se disparou. `judgments.log` >200 linhas → arquivar em `.ctxos/runtime/archive/`.
12. Confirmar em poucas linhas: nos atualizados, no(s) novo(s) se houve, drift corrigido se houve, split ocorrido se houve, decisao registrada se houve, escape do teto se houve.

Regra dura: 0 edicoes manuais nos planos entre commits — cada linha escrita aqui vem de fingerprint divergente ou do resumo do trabalho, nunca de memoria/suposicao do que "deveria" estar no index.
