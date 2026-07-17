---
description: Funil ROOT.md -> index do modulo -> arquivo/simbolo. Repara staleness via fingerprint. Devolve raio minimo de contexto pra $ARGUMENTS. Auto-invocavel so sob incerteza de localizacao.
argument-hint: "<pedido — o que voce vai mexer ou entender>"
---

Sem `.ctxos/ledger/current.md`: pare, avise "rode /ctxos:index primeiro" — locate nao bootstrapa.

Auto-invocacao (sem o usuario digitar `/ctxos:locate`): pedido ja nomeia o alvo exato ("editar Login.tsx", "corrigir a funcao parseDate em utils.ts") → NAO chamar, ja sabe onde mexer. Pedido conceitual/comportamental ("adicionar auth Google", "por que o botao de login as vezes nao aparece") → CHAMAR, alvo precisa ser localizado antes de tocar codigo.

1. Ler `.ctxos/index/ROOT.md`. Escolher modulo(s) mais provaveis pra "$ARGUMENTS".
2. Por modulo escolhido, ler `.ctxos/index/<caminho>.md`, pegar `verified:` (fingerprint).
3. Staleness: recomputar fingerprint dos arquivos-chave listados (sha256, mesma formula do index) e comparar com `verified:`. Git disponivel: `git diff --name-only` desde o ultimo commit conhecido pode acelerar QUAIS nos checar primeiro, mas fingerprint decide, nunca o diff sozinho. Divergiu → reparar esse index por INTEIRO: reler todos os arquivos-chave do no, reescrever do zero (≤15 linhas), `verified:` novo. Proibido pular reparo por achar a mudanca "so cosmetica" — fingerprint diferente e reparo integral, sempre.
4. Ainda ambiguo apos o index do modulo (simbolo especifico, arquivo dentro dele) → abrir so os arquivos-chave listados, nunca o diretorio inteiro.
5. Expandir raio: seguir arestas relevantes em `.ctxos/graph.md` a partir do(s) no(s) achado(s) — quem depende, quem e testado por, quem documenta.
6. Puxar `.ctxos/memory/<no>.md` de cada no envolvido (bloco "estado atual" se compactado, ou os ultimos Decision Records).
7. Fallback — funil nao resolveu (no novo, projeto mudou de forma que ROOT nao cobre): grep/glob direto no codigo. Declarar na saida: "via busca, index nao cobriu — no novo nasce no proximo commit". Nunca criar index aqui, so em commit.

Saida ≤12 linhas, sem prosa: no(s) + raio (arestas relevantes) + memoria relevante (1 linha por decisao) + lista minima de arquivos a carregar. Nada de recomendacao de implementacao — locate acha, nao decide.
