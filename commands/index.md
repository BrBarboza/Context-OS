---
description: Bootstrap dos 4 planos em .ctxos/ — a unica varredura ampla permitida. So roda na 1a vez ou sob pedido explicito do usuario.
disable-model-invocation: true
argument-hint: "[opcional: caminho/escopo a indexar; vazio = projeto inteiro]"
---

`.ctxos/ledger/current.md` ja existe: index reconstroi tudo do zero, caro e redundante com locate/commit incrementais — avisar e pedir confirmacao antes de seguir.

1. Listar diretorios de 1o nivel relevantes. Ignorar `.git .ctxos node_modules dist build vendor` e afins. Git disponivel: usar `git ls-files` como atalho de listagem, nunca como fonte de verdade.
2. Estimar tamanho por modulo: bytes totais / 4 ≈ tokens. Acima de ~30k tokens estimados → 1 subagente isolado por modulo, contexto proprio, so le o diretorio dele. Abaixo do limiar → thread principal le direto. Nunca leitura serial do projeto inteiro quando algum modulo estoura o limiar.
3. Por modulo, escrever `.ctxos/index/<caminho>.md` em TEMPLATE RIGIDO, sem prosa livre fora dos campos: `proposito:` (1 linha) · `arquivos:` (lista, 1 linha cada) · `entradas:` · `nao_mexer:` (se houver) · `verified:` (fingerprint). Teto 15 linhas.
4. `verified:` = fingerprint de conteudo: sha256 sobre path+conteudo de cada arquivo-chave do no, concatenados em ordem alfabetica de path. Nunca hash de commit, nunca mtime — imune a rebase/squash, funciona sem git.
5. Escrever `.ctxos/index/ROOT.md`: modulos de 1o nivel, 1 linha cada, aponta pro index de cada um.
6. Escrever `.ctxos/graph.md`: so arestas que um dev senior anotaria — import cross-modulo, estilo compartilhado, teste que cobre outro no, doc que descreve outro no. Formato `A -> B (motivo em 2-4 palavras)`, agrupado por origem.
7. Passe final da thread principal sobre todo `.ctxos/index/*.md` gerado por subagente: valida ordem/presenca dos campos, teto de linhas, nomenclatura — corrige SO estrutura, nunca reescreve o conteudo de um campo.
8. Criar `.ctxos/memory/` vazio — Decision Records nascem no commit.
9. Criar `.ctxos/runtime/state.md`: `last_scan.commit` (`git rev-parse --short HEAD` ou `no-git`) + `timestamp`, `tracked_files:` (`git ls-files | wc -l` ou `find . -type f | wc -l`). Criar `.ctxos/runtime/judgments.log` vazio — mecanismo, nunca conhecimento do projeto, por isso vive fora do ledger.
10. Escrever `.ctxos/ledger/current.md`: o que o projeto e (5 linhas) · em andamento · ultimas decisoes (vazio) · pendencias · `verified:` (fingerprint do `ROOT.md`). ≤1 pagina. So conhecimento do projeto — nada de estado de mecanismo aqui.
11. Criar `.ctxos/BACKLOG.md` SO se nao existir — unica escrita humana. Skeleton: `- [ ] <desejo> — pronto quando: <criterio mensuravel>`. Item fechado migra depois pra `.ctxos/history/<slug>.md`, 1 arquivo por item.

Nunca reexecutar index sozinho depois disso. Toda mudanca futura passa por `/ctxos:locate` e `/ctxos:commit`.
