**Experimental — fora do escopo da v1.** Congelado ate `docs/FIELD-TEST.md`
confirmar a hipotese central. Nao remover, nao evoluir, nao discutir.

# Ponte com LoopTeam (documentacao — nao implementada como dependencia)

ctxos e standalone. Os 3 comandos (`index`, `locate`, `commit`) funcionam em
qualquer projeto, com ou sem LoopTeam instalado. O que segue e um mapeamento
conceitual pra quem roda os dois plugins juntos — nao ha import, nao ha
chamada direta de um plugin no outro.

## Mapeamento

| LoopTeam | ctxos | Correspondencia |
|---|---|---|
| Lead "Mapear" (fase de reconhecimento antes de rotear pra um Dev) | `/ctxos:locate` | Onde o Lead hoje releria arquivos pra decidir o raio de um item, pode chamar locate e usar a saida (no + raio + memoria) como contexto de roteamento. |
| Lead "Registrar" (fecho de loop, pos-verificacao) | `/ctxos:commit` | Onde o Lead hoje so verifica e fecha o item, pode chamar commit com o resumo do Dev pra deixar index/grafo/memoria/ledger current. |
| `docs/BRIEFING.md` | `.ctxos/BACKLOG.md` + `.ctxos/ledger/current.md` + `.ctxos/history/<slug>.md` | BRIEFING mistura desejo humano (o que fazer) com progresso (o que ja foi). ctxos separa: BACKLOG e so desejo (fila humana), ledger/current e so estado vivo (machine-written), history/<slug> e 1 arquivo por item fechado. |

## Por que nao virou dependencia de codigo

Axioma central do ctxos: zero manutencao manual, todo plano nasce de efeito
colateral do trabalho real (diff, git log). Acoplar ctxos ao ciclo de vida
interno do LoopTeam (estado de loop, fases do Lead) criaria uma segunda fonte
de verdade pra sincronizar — exatamente o tipo de manutencao que o Axioma 0
proibe. A ponte fica documental: cada projeto decide se quer chamar locate/
commit nos pontos acima, sem o ctxos assumir que o LoopTeam existe.

## Uso combinado sugerido

1. `/loopteam:briefing` fecha o desejo → vira item(ns) em `.ctxos/BACKLOG.md`
   (copia manual pontual, ou o dono roteia direto pro BACKLOG desde o inicio).
2. Antes de rotear um item pra um Dev, Lead chama `/ctxos:locate <item>`.
3. Dev executa, loop verifica.
4. Lead chama `/ctxos:commit <resumo>` — write-through nos 4 planos.
5. Item fechado migra do BACKLOG pro `.ctxos/history/<slug-do-item>.md`
   (ctxos) e o BRIEFING (LoopTeam) marca `[x]` — duas listas, uma unica
   direcao de escrita cada.
