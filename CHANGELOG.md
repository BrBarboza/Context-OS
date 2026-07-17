# Changelog

## v0.4.0 — reset pra MVP de campo

- **Escopo travado.** README reduzido a 3 passos (instalar, index, loop
  locate→edite→commit) — sem diagramas, sem teoria, sem secao de
  arquitetura. Unica pergunta em aberto declarada: indice incremental
  reduz custo de contexto sem reduzir qualidade? Resto e consequencia
  dessa resposta.
- **Congelados como Experimental.** `commands/doctor.md`,
  `docs/ADAPTERS.md`, `docs/LOOPTEAM.md`, `docs/JUDGING.md`,
  `docs/DECISIONS.md` — banner adicionado em cada um, nenhum removido,
  nenhum evoluido, nenhuma discussao ate a v1 concluir.
- **`docs/FIELD-TEST.md` reduzido a 5 metricas.** Precisao do locate,
  tempo, tokens, drift, UX (0-10 por sessao). Metricas antigas (saltos do
  funil, raio do grafo, degradacao pos-100-commits etc.) marcadas fora do
  escopo da v1. Definition of Done explicita: 30 tarefas reais + 5
  metricas preenchidas + conclusao objetiva (confirmada/rejeitada).
- **`docs/parking-lot.md` (novo).** Ideias que surgirem durante a v1
  (scheduler, workers, supervisor, roteamento por modelo, policies,
  engine, research, telemetria) entram aqui como 1 frase, nunca
  implementadas nesta fase.
- **Mantido sem alteracao de logica:** `commands/index.md`,
  `commands/locate.md`, `commands/commit.md`, marketplace.

## v0.3.1 — destravar instalacao + instrumento de diagnostico

- **X1 — Marketplace.** Novo `.claude-plugin/marketplace.json` (repo serve
  como marketplace proprio, mesmo formato validado no LoopTeam/Caveman).
  README: secao de instalacao trocada pelos 2 comandos literais
  (`/plugin marketplace add` + `/plugin install ctxos@ctxos`) + nota de
  atualizacao (`git push` → `/plugin update` → `/reload-plugins`).
- **X2 — `/ctxos:doctor` (4o comando).** Diagnostico read-only sob demanda
  dos 4 planos + runtime: fingerprint stale (index), aresta orfa (graph),
  arquivo orfao/no sem destilacao (memory), pagina estourada/verified
  divergente (ledger), judgments.log sem arquivamento (runtime). Nunca
  repara — reparo continua sendo papel de locate/commit. Nunca recomenda
  execucao periodica (Axioma 0). Substitui de vez qualquer nocao de comando
  `audit` separado — doctor absorve o escopo. Flag `--report` pra tabela
  completa por plano.
- **X3 — Decision Record.** `docs/DECISIONS.md`: rejeitado confidence score
  continuo — fingerprint ja e confianca binaria (match/mismatch), score
  continuo exigiria calibracao permanente, o que viola o Axioma 0. Encerra
  a discussao do feedback arquitetural v0.2.

## v0.3.0 — rodada 3 de revisao dupla convergiu

- **W1 — Estado do mecanismo separado do estado do projeto.** Novo
  `.ctxos/runtime/state.md`: `last_scan` (commit+timestamp), contadores
  operacionais (`tracked_files:`). "Ultimo commit conhecido" sai do ledger
  (nunca esteve la formalmente, mas era ambiguo) e vive so aqui. Ledger
  volta a ser SO conhecimento do projeto.
- **W2 — Atalho git condicionado a porte.** `locate`/`commit`: projeto ≤200
  arquivos rastreados → ignora git, fingerprint direto em todos os
  candidatos (custo ~zero). >200 → git diff vira shortlist, fingerprint
  decide. Contagem cacheada em `runtime/state.md`.
- **W3 — Locate declara conceito novo.** Fallback do funil: grep tambem nao
  achou nada relevante → saida obrigatoria "NOVO CONCEITO — nenhum no
  existente cobre '<pedido>'". Nunca termina mudo.
- **W4 — Justificativa logada de decisao binaria de julgamento.** Novo
  `.ctxos/runtime/judgments.log` (append-only): 1 linha por decisao de
  auto-invocacao do locate e por avaliacao de drift, sim ou nao. Interno,
  nunca exibido ao usuario — calibracao futura de taxa de acerto. >200
  linhas → arquiva em `runtime/archive/`.
- **W5 — Snapshot: gatilho duplo.** `ledger/history/AAAA-MM.md` dispara por
  mes novo OU por recompactacao que corta o current em >50% das linhas —
  cobre compactacao destrutiva fora de calendario.
- **W6 — Split estrutural por efeito colateral.** No saturado — index nao
  cabe em 15 linhas sem perder informacao, OU 2o drift no MESMO no, OU
  `arquivos:` >10 — divide na mesma passada do commit que o tocou: novos
  nos herdam arquivos/entradas/arestas/memoria, ROOT atualizado, log
  "split: <no> → <herdeiro1>+<herdeiro2>". Nunca auditoria periodica.
- **Novo:** `docs/FIELD-TEST.md` — protocolo de validacao empirica (nao
  executado): alvo SaaS real ~50 arquivos, baseline ~150k tokens/sessao,
  9 metricas (M1-M9) + criterio de morte (ctxos >10-15% do custo de
  inferencia do fluxo = arquitetura falhou).

## v0.2.0 — arbitragem de revisao dupla (2 LLMs + arquiteto)

- **V1 — Fingerprint substitui commit-hash.** `verified:` agora e sha256 de
  conteudo (path+arquivo, ordem alfabetica), nao hash de commit. Imune a
  rebase/squash. Git diff vira acelerador de shortlist, nunca sensor de
  verdade. Fallback "no-git degradado" removido — sistema funciona igual
  sem git.
- **V2 — MAPA renomeia pra INDEX.** `.ctxos/map/` → `.ctxos/index/` em toda
  a spec, comandos, docs, README. Nomenclatura honesta: o arquivo localiza,
  nao descreve.
- **V3 — Limiar de subagente por tokens estimados + template rigido.**
  Modulo acima de ~30k tokens estimados (bytes/4) → subagente isolado.
  Index do no vira template de campos fixos (`proposito:`/`arquivos:`/
  `entradas:`/`nao_mexer:`/`verified:`), sem prosa livre. Passe final de
  normalizacao estrutural pela thread principal — nunca reescreve conteudo.
- **V4 — Locate auto-invocavel so sob incerteza de localizacao.** Alvo
  explicito no pedido → nao chama. Pedido conceitual/comportamental →
  chama. 2 exemplos documentados no proprio `locate.md`.
- **V5 — Reparo sempre integral.** Proibida heuristica de pular reparo por
  mudanca "so cosmetica". Fingerprint divergiu → reler e reescrever o index
  do no inteiro, sempre.
- **V6 — Teto de escrita vira soft com escape declarado.** Commit acima de
  ~20 linhas alteradas nos planos exige 1 linha de justificativa no output.
  Estouro silencioso proibido; estouro justificado permitido e logado.
- **V7 — LEDGER com snapshot mensal.** `.ctxos/LEDGER.md` vira
  `.ctxos/ledger/current.md` (≤1 pagina, como antes) +
  `.ctxos/ledger/history/AAAA-MM.md` (snapshot automatico do current antes
  da 1a compactacao de cada mes). BACKLOG fechado agora migra 1 arquivo por
  item (`.ctxos/history/<slug>.md`), nao mais um unico append-only.
- **V8 — Detector de deriva semantica.** No commit: `proposito:` de 1 linha
  que nao cobre mais semanticamente o trabalho feito no no → reescrito na
  mesma passada, logado como `drift: <no> reproposito` no output e no
  Decision Record. Deriva se corrige onde nasce, nunca vira pendencia.
- **Novo:** `docs/ADAPTERS.md` — tree-sitter/repo-maps/graphify como
  aceleradores opcionais, nunca dependencia. Markdown continua obrigatorio
  em todos os planos.

## v0.1.0 — primeira versao

- 4 planos (`map`, `graph.md`, `memory`, `LEDGER.md`) + `BACKLOG.md`.
- 3 comandos: `/ctxos:index`, `/ctxos:locate`, `/ctxos:commit`.
- `docs/LOOPTEAM.md` (ponte documental) e `docs/JUDGING.md` (rubrica).
