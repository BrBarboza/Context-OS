---
description: Write-through pos-trabalho — atualiza so os planos que o trabalho tocou, como efeito colateral. Nunca varredura ampla.
argument-hint: "<resumo do trabalho feito — o que mudou e por que>"
---

Sem `.ctxos/ledger/current.md`: pare, avise "rode /ctxos:index primeiro".

1. Candidatos a nos tocados: git disponivel → `git status --porcelain` + `git diff --name-only` desde o ultimo commit conhecido, como ATALHO de shortlist. Sem git → lista de arquivos editados nesta sessao (o proprio agente ja sabe quais tocou). Shortlist so acelera; a verdade de "tocou ou nao" vem do passo 2.
2. Pra cada no candidato, recomputar fingerprint (sha256 dos arquivos-chave, mesma formula do index) e comparar com `verified:` guardado. Divergiu → no realmente tocado, reparar por INTEIRO (nunca reparo parcial, nunca heuristica de "so cosmetica"). Igual → no intocado, nao escrever nada nele.
3. Diretorio tocado sem index → nasce agora: escrever `.ctxos/index/<caminho>.md` no TEMPLATE RIGIDO (`proposito:`/`arquivos:`/`entradas:`/`nao_mexer:`/`verified:`, ≤15 linhas) e linkar no `ROOT.md`.
4. Import/dependencia nova ou removida entre nos → atualizar `.ctxos/graph.md`: adicionar/remover a aresta `A -> B (motivo)`. Nunca recalcular o grafo inteiro.
5. Deriva semantica: `proposito:` do no tocado nao cobre semanticamente o resumo do trabalho (nenhum termo relaciona) → reescrever `proposito:` na mesma passada, logar `drift: <no> repropósito` no output e no Decision Record. Deriva se corrige onde nasce, nunca vira pendencia.
6. Houve decisao (escolha de abordagem, trade-off, rejeicao de alternativa) → append em `.ctxos/memory/<no>.md`: `[data] decisao 1 linha · motivo 1 linha · rejeitado: 1 linha`. No passou de 10 registros → destilar em bloco "estado atual" (≤6 linhas), mover atomicos pra `.ctxos/memory/archive/<no>.md`.
7. Recompactar `.ctxos/ledger/current.md` (5 secoes, `verified:` = fingerprint novo do `ROOT.md`). Antes da 1a compactacao do mes corrente → copiar o `current.md` de antes pra `.ctxos/ledger/history/AAAA-MM.md` (snapshot automatico, so 1x por mes, efeito colateral, custo 1 copia).
8. Teto ~20 linhas alteradas no total dos planos: dentro do teto → nada a declarar. Acima → permitido, mas exige 1 linha de justificativa no output ("refactor N arquivos: M indexes reescritos — legitimo"). Estouro silencioso proibido.
9. Item do BACKLOG fechado por este trabalho → mover pra `.ctxos/history/<slug-do-item>.md` (1 arquivo por item, nunca um unico append-only).
10. Confirmar em poucas linhas: nos atualizados, no(s) novo(s) se houve, drift corrigido se houve, decisao registrada se houve, escape do teto se houve.

Regra dura: 0 edicoes manuais nos planos entre commits — cada linha escrita aqui vem de fingerprint divergente ou do resumo do trabalho, nunca de memoria/suposicao do que "deveria" estar no index.
