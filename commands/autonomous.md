---
description: Ativa Autonomous Mode pro resto da sessao — usuario conversa em linguagem natural, agente orquestra locate->implementa->commit internamente sem pausar, exceto decisao arquitetural. Nunca ativo por padrao; exige chamada explicita.
disable-model-invocation: true
---

Ativa Autonomous Mode pro resto desta conversa (ate `/ctxos:manual` ser chamado — sessao nova sempre comeca em Manual).

A partir daqui, pra cada pedido em linguagem natural (sem o usuario precisar chamar `/ctxos:locate` ou `/ctxos:commit`):

1. Rodar o funil de `/ctxos:locate` internamente. Mostrar o resultado so se for relevante pro usuario acompanhar, nunca como espera de confirmacao.
2. Implementar o pedido inteiro — decompor em quantos passos internos precisar, reusar contexto entre eles, sem pausar entre itens.
3. Rodar `/ctxos:commit` internamente ao fim do trabalho (ou ao fim de cada unidade logica, se o pedido for grande e heterogeneo).
4. Entregar 1 resumo final consolidado do que foi feito.

PARAR e pedir confirmacao explicita (nunca decidir sozinho) quando o trabalho tocar:

- doc de arquitetura do projeto (README, ADR, SPEC, CHANGELOG, doc de decisao)
- config de infra, CI/CD ou pipeline de deploy
- manifesto de dependencia (`package.json`, `requirements.txt`, `Cargo.toml` e equivalentes) — so quando adiciona, remove ou rebaixa dependencia, nao em bump trivial
- schema de banco de dados ou migration
- os proprios comandos/specs do Context OS (`commands/`, `docs/` deste repo) — so se aplica quando o projeto-alvo e o proprio Context-OS
- locate devolveu NOVO CONCEITO — territorio sem no existente merece decisao humana antes de criar estrutura nova

Autonomia pra codigo dentro de nos existentes. Confirmacao pra decisao arquitetural. Bloqueio real de negocio (credencial faltando, ambiguidade que so o usuario resolve) tambem interrompe — nao e limitacao do modo, e limitacao do mundo real.

Confirmar ativacao em 1 linha: "Autonomous Mode ativo — descreva o que quer, cuido do locate/commit."
