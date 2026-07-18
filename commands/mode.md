---
description: API de sessao pro workflow — manual (controle total, para em cada etapa) ou autonomous (orquestra locate->implementa->commit sozinho, so para em decisao arquitetural). `/ctxos:manual` e `/ctxos:autonomous` sao atalhos pra este comando. Sessao nova sempre comeca em manual.
argument-hint: "manual|autonomous"
disable-model-invocation: true
---

`$ARGUMENTS` nem `manual` nem `autonomous`: nao adivinhar, mostrar uso — `/ctxos:mode manual` ou `/ctxos:mode autonomous`.

## Estado da sessao

3 variaveis vivem so na memoria desta conversa, nunca em arquivo: `Mode` (`manual`/`autonomous`, default `manual`), `Think` (`fast`/`normal`/`deep`, default `normal`, ver `/ctxos:think`), `Output` (`compact`/`verbose`, default `verbose`, ver `/ctxos:output`). Sessao nova (nenhuma ativacao ainda nesta conversa) roda nesses defaults sem o usuario precisar chamar nada.

`/ctxos:mode <valor>` atualiza SO `Mode` — `Think`/`Output` continuam com o ultimo valor ativado (ou default, se nunca setados). Uma vez `Mode` ativado, TODA mensagem seguinte nesta conversa — comando explicito ou linguagem natural, sem precisar chamar `/ctxos:mode` de novo — segue automaticamente o modo corrente abaixo, usando o `Think`/`Output` correntes nos passos indicados.

Nenhuma resposta abre com bloco de estado. Pra consultar `Mode`/`Think`/`Output` correntes (e o estado do projeto), `/ctxos:status`.

## manual

Ativa Manual Mode pro resto desta conversa (ate `/ctxos:mode autonomous`).

Pra cada pedido:

1. Rodar o funil de `/ctxos:locate`, mostrar no(s)/raio/memoria encontrados.
2. Esperar confirmacao explicita antes de tocar em qualquer arquivo.
3. Implementar so o que foi confirmado, no nivel de esforco do `Think` corrente.
4. Apos implementar, esperar confirmacao explicita antes de rodar `/ctxos:commit`.

Nunca pular uma das duas esperas, mesmo que o pedido pareca obvio. Indicado pra arquitetura, refatoracao delicada, investigacao, debugging, ou qualquer tarefa que o usuario queira acompanhar passo a passo.

## autonomous

Ativa Autonomous Mode pro resto desta conversa (ate `/ctxos:mode manual` — sessao nova sempre comeca em manual).

Pra cada pedido em linguagem natural (sem o usuario precisar chamar `/ctxos:locate` ou `/ctxos:commit`):

1. Rodar o funil de `/ctxos:locate` internamente. Mostrar o resultado so se for relevante pro usuario acompanhar, nunca como espera de confirmacao.
2. Implementar o pedido inteiro — decompor em quantos passos internos precisar, reusar contexto entre eles, sem pausar entre itens. Esforco de raciocinio segue o `Think` corrente.
3. Rodar `/ctxos:commit` internamente ao fim do trabalho (ou ao fim de cada unidade logica, se o pedido for grande e heterogeneo).
4. Entregar 1 resumo final consolidado do que foi feito, no formato do `Output` corrente.

PARAR e pedir confirmacao explicita (nunca decidir sozinho) quando o trabalho tocar:

- doc de arquitetura do projeto (README, ADR, SPEC, CHANGELOG, doc de decisao)
- config de infra, CI/CD ou pipeline de deploy
- manifesto de dependencia (`package.json`, `requirements.txt`, `Cargo.toml` e equivalentes) — so quando adiciona, remove ou rebaixa dependencia, nao em bump trivial
- schema de banco de dados ou migration
- os proprios comandos/specs do Context OS (`commands/`, `docs/` deste repo) — so se aplica quando o projeto-alvo e o proprio Context-OS
- locate devolveu NOVO CONCEITO — territorio sem no existente merece decisao humana antes de criar estrutura nova

Autonomia pra codigo dentro de nos existentes. Confirmacao pra decisao arquitetural. Bloqueio real de negocio (credencial faltando, ambiguidade que so o usuario resolve) tambem interrompe — nao e limitacao do modo, e limitacao do mundo real.

## Confirmacao

Ativar em 1 linha: "Mode: Manual — locate e commit esperam sua confirmacao." ou "Mode: Autonomous — descreva o que quer, cuido do locate/commit."

## Guardrail

`mode` so controla pausa/nao-pausa. Nunca controla quanto o agente pensa antes de implementar (isso e `/ctxos:think`) nem quanto detalhe a resposta final traz (isso e `/ctxos:output`). Estado vive na conversa, nunca em arquivo — sessao nova reseta pra `manual`, igual sempre foi.
