# Decisions

Decision Records do proprio Context OS — nao do `.ctxos/memory` de um
projeto consumidor (este repo nao roda ctxos sobre si mesmo).

## 2026-07-17 — encerrar field test da v1 antecipadamente, adotar dual-mode

Motivo: uso real no projeto Ezer mostrou que locate/commit funcionam
bem (precisao boa, indice segurou execucao longa sem correcao manual)
— o atrito observado nao foi qualidade de contexto, foi o workflow
exigir que o usuario dirigisse cada etapa manualmente. Tabela formal
de 30 tarefas/5 metricas nao foi completada; decisao se apoia em sinal
qualitativo consistente (ver "Aprendizados" em `docs/FIELD-TEST.md`),
nao no protocolo quantitativo original. v1 fecha sem dado completo por
escolha explicita, nao por falha do protocolo.

Resultado: 2 comandos novos, `/ctxos:manual` e `/ctxos:autonomous`
(`commands/manual.md`, `commands/autonomous.md`) — modos de sessao,
nao mecanismo novo. So orquestram locate/commit ja existentes, com
pausa (manual) ou sem pausa exceto decisao arquitetural (autonomous).
`docs/parking-lot.md` perde o item "execucao autonoma" (resolvido).

Escopo nao afetado: `commands/doctor.md`, `docs/ADAPTERS.md`,
`docs/LOOPTEAM.md`, `docs/JUDGING.md` continuam congelados — essa
decisao fecha so a pergunta do field test sobre index/locate/commit,
nao descongela os demais itens experimentais.

## 2026-07-17 — rejeitado: Confidence Score continuo

Motivo: fingerprint ja representa confianca de forma binaria. Match =
valido, mismatch = invalido. Score continuo exigiria calibracao permanente
— calibracao permanente implica manutencao, manutencao viola o Axioma 0
(nenhum plano exige manutencao manual recorrente). Decisao encerra a
discussao levantada no feedback arquitetural v0.2.
