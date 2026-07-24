---
description: Funil ROOT.md -> index do modulo -> arquivo/simbolo. Repara staleness via fingerprint. Devolve raio minimo de contexto pra $ARGUMENTS. Auto-invocavel so sob incerteza de localizacao. Declara NOVO CONCEITO quando nada cobre o pedido.
argument-hint: "<pedido — o que voce vai mexer ou entender>"
---

Antes de tudo: confirmar cwd real (`pwd`), nunca assumir a partir de memoria da conversa ou de projeto anterior na mesma sessao.

Sem `.ctxos/ledger/current.md` nesse cwd: pare, avise "rode /ctxos:index primeiro" — locate nao bootstrapa.

Auto-invocacao (sem o usuario digitar `/ctxos:locate`): pedido ja nomeia o alvo exato ("editar Login.tsx", "corrigir a funcao parseDate em utils.ts") → NAO chamar, ja sabe onde mexer. Pedido conceitual/comportamental ("adicionar auth Google", "por que o botao de login as vezes nao aparece") → CHAMAR, alvo precisa ser localizado antes de tocar codigo. Toda decisao, chamada ou nao, grava 1 linha em `.ctxos/runtime/judgments.log`: `[data] locate:invocado|nao-invocado motivo:"<motivo curto>"` — calibracao futura, nunca exibido ao usuario.

1. Ler `.ctxos/index/ROOT.md`. Escolher modulo(s) mais provaveis pra "$ARGUMENTS".
2. Por modulo escolhido, ler `.ctxos/index/<caminho>.md`, pegar `verified:` (fingerprint).
3. Staleness: ler `tracked_files:` em `.ctxos/runtime/state.md`. ≤200 → recomputar fingerprint direto do(s) no(s) candidato(s), ignorar git (custo ~zero). >200 → `git diff --name-only` desde `last_scan.commit` acelera qual checar primeiro, mas fingerprint decide sempre. Divergiu → reparar esse index por INTEIRO, nunca pular por achar a mudanca "so cosmetica".
4. Ainda ambiguo apos o index do modulo (simbolo especifico, arquivo dentro dele) → abrir so os arquivos-chave listados, nunca o diretorio inteiro.
5. Expandir raio: seguir arestas relevantes em `.ctxos/graph.md` a partir do(s) no(s) achado(s) — quem depende, quem e testado por, quem documenta.
6. Puxar `.ctxos/memory/<no>.md` de cada no envolvido (bloco "estado atual" se compactado, ou os ultimos Decision Records).
7. Fallback — funil nao resolveu: grep/glob direto no codigo. Achou algo → declarar "via busca, index nao cobriu — no novo nasce no proximo commit". Grep tambem nao achou nada relevante → saida obrigatoria "NOVO CONCEITO — nenhum no existente cobre '$ARGUMENTS'. O trabalho criara estrutura nova; o commit criara o(s) no(s)." Nunca terminar mudo. Nunca criar index aqui, so em commit.

## Confianca

Toda saida abre com exatamente 1 dos 3 estados abaixo — nunca percentual, nunca score continuo (rejeitado em `docs/DECISIONS.md`, 2026-07-17):

- `✓ Match` — funil resolveu 1 no/arquivo claro (fingerprint fresco ou reparado no passo 3).
- `⚠ Ambiguous — N candidatos` — passo 4 (abrir arquivos-chave) nao convergiu pra 1 alvo unico.
- `✗ No match` — cobre o branch NOVO CONCEITO do passo 7; rotulo formal do que ja era declarado em prosa.

Saida ≤12 linhas, sem prosa: estado de confianca + no(s) + raio (arestas relevantes) + memoria relevante (1 linha por decisao) + lista minima de arquivos a carregar (ou NOVO CONCEITO). Nada de recomendacao de implementacao — locate acha, nao decide.
