# Context OS (ctxos)

Infraestrutura de contexto pra LLMs trabalhando em codigo. Nao e framework de
prompts, nao e sistema de agentes: e a camada que responde "sobre o que
pensar, quando, com qual contexto" — antes de mexer em codigo e depois de
mexer.

## Axioma central

Nenhum dos 4 planos abaixo exige manutencao manual recorrente. Eles sao CACHE
do codigo+git — a verdade e sempre o codigo e o historico; os planos sao
vista materializada com politica de invalidacao. O unico arquivo que um
humano escreve e o `BACKLOG.md` (fila de desejos). Todo o resto nasce ou se
atualiza como efeito colateral de `/ctxos:index` (uma vez) e `/ctxos:commit`
(a cada trabalho).

## Os 4 planos (em `.ctxos/` do projeto onde ctxos roda)

- **`.ctxos/index/<dir>.md`** — 1 por diretorio relevante, template rigido:
  `proposito:`/`arquivos:`/`entradas:`/`nao_mexer:`/`verified:`. ≤15 linhas.
  `verified:` e fingerprint sha256 de conteudo (path+conteudo de cada
  arquivo-chave, ordem alfabetica) — nunca hash de commit, imune a
  rebase/squash, funciona sem git. `.ctxos/index/ROOT.md` indexa os modulos
  de 1o nivel.
- **`.ctxos/graph.md`** — arestas `A -> B (motivo)` entre nos do index. So
  dependencias que quebram algo se ignoradas (import, estilo compartilhado,
  teste que cobre, doc que descreve).
- **`.ctxos/memory/<no>.md`** — Decision Records atomicos por no do index.
  `>10` registros num no compacta em bloco "estado atual" + arquivo em
  `.ctxos/memory/archive/`.
- **`.ctxos/ledger/current.md`** — estado vivo do projeto, sempre ≤1 pagina,
  recompactado a cada commit. E o arquivo que torna uma sessao nova produtiva
  sozinho. `.ctxos/ledger/history/AAAA-MM.md` guarda 1 snapshot automatico
  do `current.md` por mes, tirado antes da 1a compactacao do mes.

Mais `.ctxos/BACKLOG.md` (fila humana, unica escrita manual) e
`.ctxos/history/<slug-do-item>.md` (1 arquivo por item fechado, nao relido
por padrao).

## Os 3 comandos

- **`/ctxos:index`** — bootstrap. Unica varredura ampla permitida (1a vez ou
  sob pedido explicito). Subagente isolado por modulo acima de ~30k tokens
  estimados (bytes/4). Nunca roda sozinha de novo.
- **`/ctxos:locate <pedido>`** — o coracao. Funil ROOT → index do modulo →
  arquivo/simbolo, com staleness via fingerprint (git diff so acelera a
  shortlist, nunca decide) e reparo integral a cada salto. Auto-invocavel
  so quando o pedido nao nomeia o alvo direto. Saida ≤12 linhas: no(s) +
  raio + memoria + lista minima de arquivos.
- **`/ctxos:commit <resumo>`** — write-through pos-trabalho. So atualiza nos
  cujo fingerprint divergiu. Diretorio novo → index nasce aqui, nunca em
  varredura. Detecta deriva semantica (`proposito:` que nao cobre mais o
  que o no faz) e corrige na mesma passada.

Uso manual tipico: `locate` antes de mexer, `commit` depois. Standalone —
funciona em qualquer projeto, com ou sem outros plugins, com ou sem git.

## Integracao com LoopTeam

Documentada, nao implementada como dependencia — ver `docs/LOOPTEAM.md`.
Mapear chama locate, Registrar chama commit, BRIEFING vira BACKLOG+ledger+
history. ctxos continua funcionando sem LoopTeam instalado.

## Adapters opcionais

tree-sitter/repo-maps/graphify e afins aceleram index/locate/commit quando
detectados — nunca requisito. Ver `docs/ADAPTERS.md`.

## Teste acido de independencia de LLM

Apague o plugin. Entregue so a pasta `.ctxos/` (index, graph, memory,
ledger, BACKLOG) pra um modelo cru, sem ferramentas alem de leitura de
arquivo. Ele tem que conseguir continuar o projeto lendo `ledger/current.md`
+ `index/ROOT.md` — sem o plugin, sem os comandos, sem instrucao adicional.
Se a resposta for "nao, precisa do plugin pra entender o estado", o design
falhou: tudo em `.ctxos/` e markdown convencional, sem sintaxe proprietaria,
sem campo que so o plugin sabe interpretar.

## Rubrica

Ver `docs/JUDGING.md`. Meta ≥8.8 ponderado, manutencao-zero = 10 obrigatorio.

## Instalar

Plugin local — adicionar este diretorio como marketplace local no Claude
Code (`/plugin marketplace add <caminho-desta-pasta>` ou equivalente na
configuracao de plugins) e habilitar `ctxos`. Depois disso, `/ctxos:index`
uma vez por projeto, `/ctxos:locate` e `/ctxos:commit` no dia a dia.

## Changelog

Ver `CHANGELOG.md`.
