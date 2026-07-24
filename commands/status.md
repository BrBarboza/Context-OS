---
description: Diagnostico read-only da sessao e do projeto — Mode/Think/Output correntes, saude do index, runtime derivado do modo ativo. Nunca escreve nada, nunca repara (isso e locate/commit/doctor).
disable-model-invocation: true
---

Le e reporta, nunca escreve. 3 blocos.

## SESSION

`Mode`/`Think`/`Output` correntes desta conversa — default `Manual`/`Normal`/`Verbose` se nenhum dos 3 foi chamado ainda nesta sessao.

`Mode` e `Output` ganham origem entre parenteses, na ordem de precedencia de `/ctxos:config`: `(override)` se foi setado explicitamente nesta conversa; `(profile: <nome>)` se veio de `/ctxos:config profile`; `(persistido)` se veio do arquivo `~/.ctxos/projects/<hash>/config.json` sem override nesta conversa; `(default)` se nenhum dos anteriores. `Think` nao tem origem — nunca persiste, sempre reseta pra `Normal`.

## PROJECT

Sem `.ctxos/ledger/current.md`: reportar "Nao indexado — rode /ctxos:index primeiro" e parar aqui, pular RUNTIME.

Com `.ctxos/`:

- `Index`: comparar `last_scan.commit` de `.ctxos/runtime/state.md` com `git rev-parse --short HEAD` atual. Igual → `✓ Atualizado`. Diferente → `⚠ Possivel deriva — git avancou desde o ultimo scan`. Sem git → `✓ Presente` (sem como checar deriva sem git).
- `Nodes`: contar arquivos em `.ctxos/index/*.md`, exceto `ROOT.md`.
- `Memory`: contar linhas de Decision Record (`[data] ...`) somadas em `.ctxos/memory/*.md` + `.ctxos/memory/archive/*.md`, se existir.
- `Last Commit`: `timestamp` de `.ctxos/runtime/state.md`, formatado relativo ("2 min atras", "3 dias atras").

## RUNTIME

Derivado so do `Mode` corrente, sem estado proprio:

- `Mode = autonomous`: `Locate` e `Commit` = `Ativo (automatico)`, `Autonomous` = `Ativo`.
- `Mode = manual`: `Locate` e `Commit` = `Manual (aguarda confirmacao)`, `Autonomous` = `Inativo`.

## Formato

```
Context OS

SESSION
────────────────────────
Mode.............<Manual|Autonomous> (<override|profile: nome|persistido|default>)
Think............<Fast|Normal|Deep>
Output...........<Compact|Verbose> (<override|profile: nome|persistido|default>)

PROJECT
────────────────────────
Index............<✓ Atualizado|⚠ Possivel deriva|✓ Presente>
Nodes............<N>
Memory...........<N> decisoes
Last Commit......<relativo>

RUNTIME
────────────────────────
Locate...........<Ativo (automatico)|Manual (aguarda confirmacao)>
Commit...........<Ativo (automatico)|Manual (aguarda confirmacao)>
Autonomous.......<Ativo|Inativo>
```

## Guardrail

So leitura — nunca recomputa fingerprint por INTEIRO (isso e `/ctxos:doctor`, mais caro), nunca repara nada (isso e `/ctxos:locate`/`/ctxos:commit`). Nao persiste nada, nao cria arquivo. Fora de escopo: sem metrica de token/custo/tempo de sessao, sem painel grafico — fica em `docs/parking-lot.md` pra evolucao futura.
