# Context OS (ctxos)

Plugin do Claude Code que da contexto de codigo pro agente sem ele
precisar varrer o projeto inteiro toda vez.

## O que e

Um mapa incremental do seu projeto, guardado em `.ctxos/`. Cada
diretorio relevante vira um "no" — um arquivo curto (`.ctxos/index/
<caminho>.md`) que resume pra que serve aquele pedaco de codigo, quais
arquivos importam e com o que ele se conecta. O agente le esse mapa
antes de mexer em qualquer coisa, em vez de ler o repositorio inteiro.

## Problema que resolve

Sem esse mapa, o LLM tem duas opcoes ruins: varrer o codigo inteiro
toda tarefa (caro, lento, estoura contexto em projetos grandes) ou
trabalhar as cegas com pouco contexto (erra o arquivo, erra a funcao).
Ctxos mantem o mapa atualizado incrementalmente pra devolver so o
pedaco certo, toda vez, sem reescanear o projeto do zero.

## Como funciona (3 etapas)

1. **index** — roda uma vez, no inicio do projeto. Varre tudo e cria
   o mapa em `.ctxos/` (um "no" por diretorio relevante).
2. **locate** — a cada pedido, le so o mapa (nunca o codigo inteiro),
   acha o(s) no(s) relacionados e devolve pro agente uma lista minima
   de arquivos pra abrir.
3. **commit** — depois que o agente mexeu no codigo, atualiza so os
   nos que a tarefa tocou (nunca o mapa inteiro) e registra as
   decisoes tomadas.

Exemplo pratico:

```
/ctxos:locate "adicionar botao de logout no header"
→ no: src/components/Header
→ arquivos: Header.tsx, useAuth.ts
→ memoria: "logout ja usa useAuth().signOut(), nao criar hook novo"
```

## Instalar

Requer [Claude Code](https://claude.com/claude-code) com plugins
habilitados.

```bash
/plugin marketplace add BrBarboza/Context-OS
/plugin install ctxos@ctxos
```

Alterou o plugin?

```bash
git push
/plugin update
/reload-plugins
```

## Fluxo recomendado

`index` roda uma vez. Depois disso, todo pedido segue esse ciclo:

```
/ctxos:locate <pedido>
   ↓
(desenvolvimento livre pelo agente)
   ↓
/ctxos:commit <resumo>
```

O desenvolvimento entre locate e commit pode acontecer numa unica
execucao longa. Dado contexto suficiente do locate e uma autorizacao
explicita ("pode implementar tudo"), o agente decompoe a tarefa
internamente, implementa varios itens em sequencia e so interrompe em
bloqueios reais de negocio. Context OS nao exige que o usuario
trabalhe em microetapas nem confirme cada passo.

## Filosofia

- **indice incremental** — nunca varredura ampla fora do index inicial.
- **contexto minimo** — locate devolve so o raio necessario, nao o
  projeto inteiro.
- **memoria evolutiva** — decisoes ficam registradas nos nos que
  tocam, cresce junto com o projeto.
- **sem contexto infinito** — nada de acumular tudo pra sempre; no
  saturado faz split.

## Field Test

v1 esta em validacao empirica. Protocolo completo, metricas e
criterio de encerramento em `docs/FIELD-TEST.md`.

Unica pergunta em aberto: **o indice incremental reduz o custo de
contexto sem reduzir a qualidade das entregas?** Ainda nao confirmada.

Encerra quando 30 tarefas reais estiverem registradas, as 5 metricas
preenchidas, e houver conclusao objetiva — hipotese confirmada ou
rejeitada. Nenhuma decisao de arquitetura (produto, scheduler, comando
novo) e tomada antes disso.

Aprendizados parciais (fatos observados, sem conclusao arquitetural)
tambem ficam em `docs/FIELD-TEST.md`.

**Context OS esta em validacao. Use, meca, e so depois evolua.**

## Escopo congelado (Experimental)

Fora do escopo desta fase, congelado, nao evolui ate a pergunta do
field test ter resposta:

- `/ctxos:doctor` (`commands/doctor.md`)
- Adapters opcionais (`docs/ADAPTERS.md`)
- Integracao LoopTeam (`docs/LOOPTEAM.md`)
- Rubrica de julgamento (`docs/JUDGING.md`)
- Decisoes futuras (`docs/DECISIONS.md`)

Ideias fora de escopo (scheduler, workers, supervisor, roteamento por
modelo, policies, engine, research, telemetria, execucao autonoma)
ficam em `docs/parking-lot.md`, nao aqui.

## Changelog

Ver `CHANGELOG.md`.
