# Decisions

Decision Records do proprio Context OS — nao do `.ctxos/memory` de um
projeto consumidor (este repo nao roda ctxos sobre si mesmo).

## 2026-07-24 — Fingerprint binario no locate, versao restrita de Confianca

Problema: `docs/VISION.md` propunha "Confianca" como linha de pesquisa —
expor o proprio fingerprint (match/mismatch) do locate como sinal de
certeza, em vez de silencio. Faltava decidir o formato exato de
superficie.

Alternativas consideradas: score continuo (percentual) — ja rejeitado
em 2026-07-17 (calibracao permanente viola Axioma 0, usuario sempre
pergunta "por que 73 e nao 71"); pipeline que decide sozinho se mostra
ou nao o estado — reintroduziria heuristica de fluxo sem dado coletado,
mesma categoria de risco do `think` (Axioma 3).

Decisao: locate abre toda saida com exatamente 1 de 3 estados textuais —
`✓ Match`, `⚠ Ambiguous — N candidatos`, `✗ No match` (rotulo formal do
branch NOVO CONCEITO ja existente). Sem percentual, sem escala. Nao
soma linha ao teto de ≤12 linhas — substitui silencio implicito por 1
linha explicita.

Consequencia: `commands/locate.md` ganha secao "Confianca" antes do
contrato de saida. `docs/VISION.md` marca a linha de pesquisa Confianca
como implementada nesta forma restrita — score continuo e pipeline
adaptativo por confianca continuam fora, em categorias diferentes (o
primeiro rejeitado, o segundo Research/Hipotese).

## 2026-07-24 — `/ctxos:config` absorve mode/output; mode/output viram Compatibility Commands

Motivo: uso real mostrou friccao — `mode`/`output`/`think` como 3
comandos soltos (v0.6.0) cresce mal a cada eixo novo, e usuario
reconfigura os mesmos 2 valores toda sessao nova. Mesmo precedente ja
usado em 2026-07-18 (mode absorveu manual/autonomous): comando novo
vira fonte unica, comandos antigos viram redirecionamento fino.

Decisao: `commands/config.md` (novo) absorve a logica hoje em
`commands/mode.md` + `commands/output.md`, mais um eixo novo `profile`
(preset opinativo que seta mode+output de uma vez — ver tabela no
proprio arquivo) e a logica de persistencia (proxima entrada).
`mode.md`/`output.md` colapsam pra alias de 1 linha, mesmo formato que
`manual.md`/`autonomous.md` ja usam.

Terminologia: chamamos `mode`/`output`/`manual`/`autonomous` de
**Compatibility Commands**, nao "alias permanente" — evita a leitura de
"promessa eterna imutavel"; permite no futuro dizer "continuam
suportados" ou "entraram em modo legado" sem soar quebra de contrato.
Por ora: sem aviso de deprecated, sem remocao planejada, mesmo
precedente de manual/autonomous ("nunca removidos, nunca quebrando
doc/uso existente").

`think` fica de fora do `config` unificado — continua eixo separado e
Experimental (Axioma 3); misturar no mesmo comando promoveria ele de
volta a eixo igual aos outros, contradizendo a conclusao do field test.

## 2026-07-24 — Persistencia de Mode/Output fora do repo, por projeto

Motivo: 2026-07-18 decidiu deliberadamente NAO persistir mode/think/
output em arquivo, porque um arquivo em `.ctxos/runtime/` seria
commitado no repo do projeto-alvo — 2a fonte de verdade pra preferencia
pessoal, vazando de um dev pro outro via git. Uso real confirmou o
custo do outro lado: sem persistencia nenhuma, `/clear` (ou sessao
nova) reseta Mode/Output toda vez, e usuario reconfigura os mesmos
valores repetidamente.

Alternativas consideradas: `.ctxos/config.json` dentro do repo (mesmo
problema de 2026-07-18, so muda o nome do arquivo); persistir em
`.ctxos/runtime/` com `.gitignore` automatico (fragil — depende do
usuario nao versionar `.ctxos/` inteiro, ou de gitignore ja existente
cobrir o caminho certo).

Decisao: preferencia pessoal vive fora do repo, em
`~/.ctxos/projects/<hash>/config.json` (`<hash>` = 12 hex de sha256 do
caminho absoluto do projeto — mesma primitiva de fingerprint que
index/locate/commit ja usam). Nunca git, nunca 2a fonte de verdade do
projeto — chave por projeto (hash do path) permite 2 projetos com
config independente na mesma maquina. Leitura lazy (so na 1a invocacao
de um comando ctxos na conversa, nunca no inicio incondicional); escrita
write-through a cada `mode`/`output`/`profile` setado.

Consequencia: reforca a decisao de 2026-07-18 em vez de contraria-la —
o problema evitado (preferencia pessoal virando estado do projeto) so
existia por causa de ONDE o arquivo ficava, nao por persistir ou nao.
`think` continua sem persistencia (nunca ganhou pedido de uso diario
suficiente pra justificar, e continua Experimental).

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
