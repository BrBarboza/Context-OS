# Context OS (ctxos)

Infraestrutura de contexto pra LLMs trabalhando em codigo.

## Instalar

```bash
/plugin marketplace add BrBarboza/Context-OS
/plugin install ctxos@ctxos
```

## Usar

1. `/ctxos:index` — uma vez, no inicio do projeto (ou quando pedir explicitamente de novo).
2. Pra mexer em algo:

```
/ctxos:locate <pedido>
   ↓
edite
   ↓
/ctxos:commit <resumo>
```

Repete a cada tarefa.

Alterou o plugin?

```bash
git push
/plugin update
/reload-plugins
```

## Status — v1, MVP de campo

Unica pergunta em aberto: **indice incremental reduz custo de contexto sem
reduzir qualidade das entregas?** Ainda nao confirmada — protocolo e
metricas em `docs/FIELD-TEST.md`.

Nada abaixo faz parte do escopo desta fase — marcado **Experimental**,
congelado, nao evolui ate a pergunta acima ter resposta:

- `/ctxos:doctor` (`commands/doctor.md`)
- Adapters opcionais (`docs/ADAPTERS.md`)
- Integracao LoopTeam (`docs/LOOPTEAM.md`)
- Rubrica de julgamento (`docs/JUDGING.md`)
- Decisoes futuras (`docs/DECISIONS.md`)

Ideias fora de escopo (scheduler, workers, supervisor, roteamento por
modelo, policies, engine, research, telemetria) ficam em
`docs/parking-lot.md`, nao aqui.

## Changelog

Ver `CHANGELOG.md`.
