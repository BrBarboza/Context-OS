# Vision — depois do field test v0.6

Este doc registra pra onde o Context OS aponta depois da conclusao do
field test do eixo `think` (ver `docs/DECISIONS.md`, Axioma 3). Diferente
de `docs/parking-lot.md` ("talvez um dia"), as linhas abaixo tem
paternidade clara: sao consequencia direta de um experimento medido, nao
ideia solta.

## Hipotese confirmada

A qualidade do Context OS vem de contexto estruturado — index/locate/
commit curando o que o modelo ve. Nao vem de aumentar o esforco cognitivo
do modelo (`think` testou isso e falhou nos dados reais).

Pergunta que passa a guiar evolucao do projeto: nao "como fazer o agente
pensar mais", e sim **"como aumentar qualidade sem aumentar pensamento"**
— informacao melhor no lugar de mais deliberacao.

## Proximas linhas de pesquisa

Nenhuma das 5 abaixo esta implementada. Status `Hipotese` = precisa do
mesmo rigor que derrubou `think`: definir o que medir, medir com tarefa
real, so virar comando com dado a favor.

### Relacoes

Locate expõe nao so arquivos, mas dependencias ja conhecidas (import
cross-modulo, quem chama quem) direto no resultado do funil — dado que
`graph.md` ja calcula, so nao e mostrado hoje.

Status: Hipotese. Custo de validar: baixo (dado ja existe em `graph.md`,
so precisa expor).

### Confianca

Locate expõe o proprio fingerprint (match/mismatch) como sinal de
confianca no resultado — "98%" ou "42% — ha 2 componentes parecidos" em
vez de silencio sobre certeza.

Status: Hipotese. Ja foi discutido e rejeitado uma vez como score continuo
(ver DECISIONS.md 2026-07-17) — essa versao e mais restrita, expõe o
binario existente, nao inventa metrica nova.

### Historico

Antes de editar um no, mostrar quantas vezes ele ja mudou e as ultimas
decisoes relevantes de `.ctxos/memory/<no>.md` — contexto de "esse
componente e sensivel" antes de tocar.

Status: Hipotese. Custo de validar: baixo, dado ja existe em memory.

### Impacto

Antes de editar, estimar raio de blast — quantos componentes/rotas/
schemas provavelmente sao tocados pela mudanca, via arestas de
`graph.md`.

Status: Hipotese. Custo de validar: medio — exige traversal, nao so
leitura direta.

### Locate inteligente

Funil devolve nao so arquivo, mas simbolos relacionados relevantes
(hooks, providers, componentes vizinhos) no mesmo resultado — reduz
segunda rodada de busca.

Status: Hipotese. Custo de validar: medio-alto — pode tensionar com teto
de ≤12 linhas do locate (ver `docs/JUDGING.md`), precisa desenho proprio
antes de testar.

## Categorias do projeto

- **Core (estavel)** — `index`, `locate`, `commit`, `mode`, `output`. Definem o produto, tem dado real a favor.
- **Experimental** — `think`, `doctor`, `adapters`, `loopteam`, `judging`. Existem, funcionam, mas sem validacao suficiente pra virar Core (ou, no caso de `think`, ja invalidados).
- **Research** — confianca, relacoes, impacto, historico, locate inteligente. Nao sao features, sao linhas de pesquisa — nascem daqui, so viram Experimental depois de desenho + primeiro teste real.
