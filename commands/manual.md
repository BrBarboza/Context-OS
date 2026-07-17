---
description: Ativa Manual Mode pro resto da sessao — controle total, agente para e espera confirmacao antes de implementar e antes de commitar. Modo padrao: toda sessao nova comeca aqui ate o usuario chamar /ctxos:autonomous.
disable-model-invocation: true
---

Ativa Manual Mode pro resto desta conversa (ate `/ctxos:autonomous` ser chamado).

A partir daqui, pra cada pedido:

1. Rodar o funil de `/ctxos:locate` pro pedido, mostrar no(s)/raio/memoria encontrados.
2. Esperar confirmacao explicita do usuario antes de tocar em qualquer arquivo.
3. Implementar so o que foi confirmado.
4. Apos implementar, esperar confirmacao explicita antes de rodar `/ctxos:commit`.

Nunca pular uma das duas esperas nesse modo, mesmo que o pedido pareca obvio. Indicado pra arquitetura, refatoracao delicada, investigacao, debugging, ou qualquer tarefa que o usuario queira acompanhar passo a passo.

Confirmar ativacao em 1 linha: "Manual Mode ativo — locate e commit esperam sua confirmacao."
