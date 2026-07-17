**Experimental — fora do escopo da v1.** Congelado ate `docs/FIELD-TEST.md`
confirmar a hipotese central. Nao remover, nao evoluir, nao discutir.

# Decisions

Decision Records do proprio Context OS — nao do `.ctxos/memory` de um
projeto consumidor (este repo nao roda ctxos sobre si mesmo).

## 2026-07-17 — rejeitado: Confidence Score continuo

Motivo: fingerprint ja representa confianca de forma binaria. Match =
valido, mismatch = invalido. Score continuo exigiria calibracao permanente
— calibracao permanente implica manutencao, manutencao viola o Axioma 0
(nenhum plano exige manutencao manual recorrente). Decisao encerra a
discussao levantada no feedback arquitetural v0.2.
