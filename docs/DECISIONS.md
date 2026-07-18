# Decisions

Decision Records do proprio Context OS — nao do `.ctxos/memory` de um
projeto consumidor (este repo nao roda ctxos sobre si mesmo).

## 2026-07-18 — header removido, `/ctxos:status` vira diagnostico sob demanda

Motivo: header `CTXOS / Mode / Think / Output` cumpriu papel de validacao
durante os testes iniciais, mas poluir toda resposta operacional com estado
nao e a UX desejada pro uso diario. Estado consultado sob demanda, nao
empurrado em toda resposta.

Resultado: `commands/status.md` (novo) — 3 blocos (SESSION/PROJECT/RUNTIME),
so leitura, nunca repara. `mode.md`/`output.md` perderam a instrucao de
emitir header; comportamento de mode/think/output nao muda, so a
apresentacao. Metrica de token/custo/tempo dentro do status ficou de fora
desta rodada (parking-lot) — mesma razao ja registrada abaixo pro painel
estilo Ruflo: modelo nao tem acesso confiavel a numero real de custo/tempo.

## 2026-07-18 — mode vira API de sessao, ganha think/output como eixos irmaos

Motivo: critica inicial propunha manter `/ctxos:manual`/`/ctxos:autonomous`
como comandos soltos e adicionar `think`/`output` tambem soltos — ergonomia
minima pra v0.5, mas sem forma consistente de crescer (proximo eixo viraria
mais um comando com convencao propria). Decisao do dono do projeto: `mode`
deixa de ser so alias de manual/autonomous e vira o padrao que toda
configuracao de sessao segue (`/ctxos:<eixo> <valor>`), com
`/ctxos:manual`/`/ctxos:autonomous` mantidos como atalhos — nunca removidos,
nunca quebrando doc/uso existente. Precedente: Git faz o mesmo (varios
comandos de superficie sao alias de forma canonica interna).

Resultado: `commands/mode.md` (novo, fonte unica de verdade do workflow,
absorve o que antes vivia em `manual.md`/`autonomous.md`), `commands/think.md`
(novo) e `commands/output.md` (novo). `manual.md`/`autonomous.md` viram
atalho de 1 linha. Guardrails mantidos da critica original: think/output
nunca tocam mecanica estrutural de locate/commit, nunca introduzem pausa
(so mode controla pausa), nunca ganham estado persistido em arquivo (mesma
razao que mode ja seguia desde v0.5.0 — arquivo de sessao em
`.ctxos/runtime/` seria commitado no projeto-alvo, virando 2a fonte de
verdade pra preferencia pessoal).

Nao adotado desta rodada: persistir mode/think/output em arquivo pro
statusline do Claude Code renderizar metricas de sessao (tokens/custo/painel
visual estilo Ruflo) — fica em `docs/parking-lot.md`, e subsistema
proprio, nao knob de comando.

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
