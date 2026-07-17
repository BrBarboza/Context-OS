# Changelog

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
