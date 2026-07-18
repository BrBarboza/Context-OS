# Decisions

Decision Records do proprio Context OS — nao do `.ctxos/memory` de um
projeto consumidor (este repo nao roda ctxos sobre si mesmo).

## 2026-07-18 — Axioma 3: Context OS controla o ambiente, nunca o modelo; think vira Experimental

Motivo: field test v0.6 do eixo `think` (fast/normal/deep) falseou a
hipotese que fundou o comando — fast custou mais tempo E mais token que
normal (7m31s/32.1k vs 3m33s/17.6k), deep gastou mais token sem ganho de
qualidade proporcional. Analise: `think` e o unico comando do ctxos que
tenta controlar processo interno do modelo (orcamento cognitivo), nao
ambiente externo — categoria estruturalmente diferente de
index/locate/commit/mode/output, que sempre controlaram contexto/fluxo/
memoria. Resultado formaliza principio:

> **Axioma 3 — Context OS controla o ambiente, nunca o modelo.**
>
> O Context OS atua exclusivamente sobre elementos externos ao modelo:
> contexto, fluxo de execucao e memoria do projeto. Nao tenta controlar
> orcamento cognitivo, cadeia de pensamento ou esforco interno de
> raciocinio do LLM. Sempre que possivel, a melhoria de qualidade deve vir
> de informacao melhor, e nao de mais deliberacao.

Resultado: `/ctxos:think` marcado `Status: Experimental` em
`commands/think.md` — nao removido, resultado do field test documentado no
proprio comando (deep/fast, achados, conclusao). Mantido so como registro
de pesquisa, nao recomendado pra uso diario; reavaliar so com modelo novo
ou dado novo. `/ctxos:output` NAO e afetado pelo Axioma 3 — formata texto
final (superficie de geracao), categoria diferente de "esforco de
raciocinio"; `/ctxos:mode` tambem nao e afetado — controla pausa/nao-pausa,
checkpoint binario no ambiente, nao processo interno.

Consequencia: as 5 linhas de pesquisa que emergem dessa conclusao
(relacoes, confianca, historico, impacto, locate inteligente) NAO entram
no `parking-lot.md` generico ("talvez um dia") — tem paternidade clara
(consequencia direta do field test v0.6) e ganham documento proprio,
`docs/VISION.md`.

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
