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

4 das 5 abaixo continuam so hipotese. Status `Hipotese` = precisa do
mesmo rigor que derrubou `think`: definir o que medir, medir com tarefa
real, so virar comando com dado a favor. A 5a — Confianca — teve sua
versao restrita (fingerprint binario) adotada em v0.7.0; o que sobra
dela (score continuo, pipeline adaptativo por confianca) continua
Hipotese, ver secao propria abaixo.

### Relacoes

Locate expõe nao so arquivos, mas dependencias ja conhecidas (import
cross-modulo, quem chama quem) direto no resultado do funil — dado que
`graph.md` ja calcula, so nao e mostrado hoje.

Status: Hipotese. Custo de validar: baixo (dado ja existe em `graph.md`,
so precisa expor).

### Confianca

Locate expõe o proprio fingerprint (match/mismatch) como sinal de
confianca no resultado, em vez de silencio sobre certeza.

**Implementado (v0.7.0):** versao restrita, binaria — `✓ Match` /
`⚠ Ambiguous — N candidatos` / `✗ No match`. Ver `commands/locate.md` e
`docs/DECISIONS.md` (2026-07-24). Nao inventa metrica nova, so expoe o
que o funil ja calcula.

**Research (continua Hipotese):**
- Confidence continuo/percentual ("98%", "42%") — rejeitado uma vez
  (DECISIONS.md 2026-07-17) e permanece rejeitado: calibracao permanente
  viola Axioma 0. Nao e "ainda nao fizemos", e "decidimos nao fazer".
- Pipeline adaptativo — pular `/ctxos:locate` quando confianca alta o
  suficiente pra dispensar. Nao vira comando sem medir primeiro: contar
  em `.ctxos/runtime/judgments.log` (ja existe, ja registra
  `invocado`/`nao-invocado` por motivo) quantas invocacoes reais eram
  dispensaveis antes de desenhar qualquer skip automatico.

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

- **Core (estavel)** — `index`, `locate` (com fingerprint binario), `commit`, `config` (mode/output/profile, absorve `mode`/`output` como Compatibility Commands), fingerprint binario de confianca. Definem o produto, tem dado real a favor.
- **Experimental** — `think`, `doctor`, `adapters`, `loopteam`, `judging`. Existem, funcionam, mas sem validacao suficiente pra virar Core (ou, no caso de `think`, ja invalidados).
- **Research** — confianca continua/pipeline adaptativo, relacoes, impacto, historico, locate inteligente. Nao sao features, sao linhas de pesquisa — nascem daqui, so viram Experimental depois de desenho + primeiro teste real.

## Mudanca de filosofia — v0.7.0

v0.5.0 a v0.6.2 evoluiram o projeto principalmente em **capacidade**:
comando novo por eixo novo (`manual`/`autonomous`, depois `mode`/`think`/
`output` separados, depois `status`). v0.7.0 inverte a prioridade —
**ergonomia** em vez de superficie nova: `mode`+`output` viram 1 comando
(`config`), preferencia passa a persistir (menos repeticao entre
sessoes), locate ganha 1 linha de sinal em vez de silencio. Nenhum eixo
novo nasceu nesta rodada.

Registrado aqui pra evitar reverter o ganho no futuro: proximo eixo de
configuracao entra em `/ctxos:config` (ou sucessor), nao vira comando
solto novo — refragmentar a superficie pra resolver algo que um comando
unificado ja cobre repete o erro que esta rodada corrigiu.
