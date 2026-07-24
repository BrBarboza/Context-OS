<p align="center">
  <img src="assets/ctxos-banner.png" alt="Context OS Banner" width="100%">
</p>

<p align="center">
  Faça seu agente de IA trabalhar como se já conhecesse seu projeto.
</p>

<p align="center">

[![Marketplace](https://img.shields.io/badge/Claude%20Marketplace-Context%20OS-orange)](https://github.com/BrBarboza/Context-OS)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
![Claude Code](https://img.shields.io/badge/Claude-Code-orange)
![Version](https://img.shields.io/badge/version-v0.7.0-success)
![Status](https://img.shields.io/badge/status-Stable-brightgreen)

</p>

---

O **Context OS** é um plugin para o Claude Code que cria um mapa inteligente do seu código, pra que a IA nunca mais precise redescobrir o projeto do zero a cada pedido.

---

# Por que existe?

Toda vez que você pede uma alteração para uma IA, ela precisa descobrir coisas como:

- Onde fica essa funcionalidade?
- Quais arquivos estão relacionados?
- O que pode ser alterado?
- Quais decisões arquiteturais já foram tomadas?
- O que não deve ser quebrado?

Sem contexto, boa parte do esforço da IA é simplesmente entender o projeto novamente.

O **Context OS** transforma esse processo em conhecimento permanente — o agente passa a trabalhar como alguém que já conhece a base de código.

---

# Como funciona

Durante o desenvolvimento ele mantém um mapa vivo do projeto — o que localiza apenas o código relevante pra cada pedido, lembra decisões já tomadas, e atualiza esse conhecimento sozinho conforme o projeto muda.

---

# Instalação

```bash
/plugin marketplace add BrBarboza/Context-OS
/plugin install ctxos@ctxos
```

---

# Primeiros passos

Indexe o projeto apenas uma vez.

```bash
/ctxos:index
```

Escolha como você quer trabalhar (ver abaixo) e pronto — descreva o que quer, o ctxos cuida do resto.

---

# Como você quer trabalhar?

```bash
/ctxos:config profile backend
```

| Profile | | Quando usar |
|---|---|---|
| **`backend`** | ✓ trabalha sozinho · ✓ respostas curtas | CRUD, API, banco de dados, lote de tarefas rápidas |
| **`frontend`** | ✓ trabalha sozinho · ✓ explica alterações | Mudança de UI, quer acompanhar o que mudou visualmente |
| **`architect`** | ✓ pede confirmação · ✓ explica tudo | Refactor grande, mudança estrutural, decisão arquitetural |
| **`reviewer`** | ✓ pede confirmação · ✓ respostas curtas | Acompanhar passo a passo sem prosa |

Escolheu um, acabou. Preferência persiste — `/clear` ou sessão nova no mesmo projeto retomam o mesmo profile.

Quer misturar comportamentos manualmente em vez de usar um preset? Ver **Configuração avançada** no fim.

---

# Exemplos de uso

## Trabalha sozinho (`backend`/`frontend`)

```text
Remove a navbar da tela de checkout.
```

O ctxos executa locate → implementação → commit sozinho, sem novos comandos. Só interrompe pra decisão que foge do código — banco de dados, infraestrutura, CI/CD, nova dependência, novo conceito arquitetural.

## Pede confirmação (`architect`/`reviewer`)

```text
Você

/ctxos:locate "Por que o login falha às vezes?"

↓

ctxos

✓ Match
Arquivos encontrados:
- auth.ts
- login.tsx
- session.ts

Aguardando autorização.

↓

Você

Pode implementar.

↓

ctxos

Implementado. Aguardando commit.

↓

Você

/ctxos:commit
```

---

# Status

Consulta o estado atual sem precisar procurar nas respostas:

```bash
/ctxos:status
```

```text
Context OS

SESSION
────────────────────────
Mode.............Autonomous (persistido)
Think............Deep
Output...........Compact (profile: backend)

PROJECT
────────────────────────
Index............✓ Atualizado
Nodes............42
Memory...........18 decisões
Last Commit......2 min atrás

RUNTIME
────────────────────────
Locate...........Ativo
Commit...........Ativo
Autonomous.......Ativo
```

---

# Benchmark

O Context OS foi testado implementando exatamente o mesmo aplicativo, com e sem ctxos.

| | Claude Code (normal) | Claude Code + Context OS |
|---|---|---|
| Interações | 46 | 91 |
| Tempo | 7min 39s | 23min 12s |
| Tokens | 117,9 mil | 381,7 mil |
| Custo | US$ 3,32 | US$ 10,54 |
| Resultado | Boa aplicação, ainda exigiria horas de ajuste manual | Arquitetura reorganizada, componentes extraídos, docs e decisões registradas — praticamente pronto pra continuidade |

## Conclusão

O ctxos consumiu ~98% mais interações, ~203% mais tempo, ~224% mais tokens, ~217% mais custo — e ainda assim entregou um resultado muito superior.

> **O Context OS não faz a IA trabalhar menos. Faz a IA trabalhar melhor.**

---

# Configuração avançada

`profile` é preset de 2 eixos independentes — mexa neles direto se quiser combinação fora da tabela acima:

```bash
/ctxos:config mode manual
/ctxos:config mode autonomous

/ctxos:config output compact
/ctxos:config output verbose
```

- **Mode** — Manual (espera autorização a cada etapa) ou Autonomous (orquestra sozinho).
- **Output** — `compact` é checklist enxuto; `verbose` explica decisões, nós e memória tocados.
- **Think** *(experimental)* — quanto o agente raciocina antes de implementar. Field test não achou benefício prático, fica fora do `config` unificado — ver `docs/VISION.md`.

`/ctxos:mode`, `/ctxos:output`, `/ctxos:manual` e `/ctxos:autonomous` continuam funcionando — são Compatibility Commands, redirecionam pra `/ctxos:config` por baixo, sem prazo de remoção.

> **Persistência:** Mode e Output são preferência pessoal e ficam fora do repositório (`~/.ctxos/projects/<hash>/config.json`). Sobrevivem a `/clear`, nunca vão pro Git, nunca são levados num clone em outra máquina. `Think` nunca persiste.

---

# Roadmap

Em pesquisa, fora do core estável (`index`/`locate`/`commit`/`config`/`status`):

- **Think** — ver `docs/VISION.md`
- **Doctor**
- **Adapters**
- **Integração com LoopTeam**

---

# Licença

MIT
