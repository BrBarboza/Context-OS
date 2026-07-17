# Context OS (ctxos)

## O que e

Plugin do Claude Code que da ao agente so o contexto de codigo que ele
precisa pra cada tarefa — em vez dele ler o projeto inteiro (caro,
lento) ou trabalhar as cegas (erra o alvo). Resultado: respostas mais
rapidas, mais precisas, e um agente que lembra decisoes ja tomadas no
seu projeto.

## Como instalar

```bash
/plugin marketplace add BrBarboza/Context-OS
/plugin install ctxos@ctxos
```

## Primeiros 30 segundos

```bash
/ctxos:index          # 1x, no inicio do projeto
/ctxos:manual         # controle total (padrao)
/ctxos:autonomous     # conversa normal, agente cuida do resto
```

So isso pra comecar. O resto e detalhe.

## Manual Mode

Use quando quer acompanhar cada passo: arquitetura, refatoracao
delicada, investigacao, debugging.

```
voce:  /ctxos:locate "por que o login as vezes nao redireciona"
ctxos: no achado, arquivos X e Y — aguardando confirmacao pra mexer
voce:  pode implementar
ctxos: implementado — aguardando confirmacao pro commit
voce:  /ctxos:commit "corrige redirect condicional no login"
```

Cada etapa espera voce.

## Autonomous Mode

Use no dia a dia: pedidos concretos, feature pequena/media, bug
conhecido. Voce so conversa — nao precisa chamar locate nem commit.

```
voce:  remove a navbar da pagina de checkout
ctxos: [trabalha sozinho]
ctxos: feito — navbar removida em Checkout.tsx, testes passando,
       commit registrado
```

Autonomous para e pergunta so quando a mudanca sai do codigo e entra
em decisao de arquitetura (dependencia nova, schema de banco, CI/CD,
docs do projeto). Pra codigo dentro do que ja existe, ele decide e
entrega.

## Filosofia

Manual = controle. Autonomous = velocidade. Mesmo mecanismo por
baixo — o que muda e quanto voce quer ser consultado no meio do
caminho.

## O que acontece internamente

Voce nao precisa saber disso pra usar a ferramenta — mas se quiser
entender: Autonomous roda, pra cada pedido,

```
locate (acha o codigo certo)
   ↓
implementacao
   ↓
commit (atualiza o mapa do projeto)
```

automaticamente, sem pausar entre as etapas. Manual roda o mesmo
funil, so que parando antes de implementar e antes de commitar.

## Fora de escopo

`/ctxos:doctor`, adapters, integracao LoopTeam e rubrica de julgamento
seguem congelados como experimental — nao fazem parte do uso normal.
Ideias novas fora de escopo vao pra `docs/parking-lot.md`. Decisoes
arquiteturais do proprio ctxos ficam registradas em
`docs/DECISIONS.md`.

## Changelog

Ver `CHANGELOG.md`.
