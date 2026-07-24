---
description: API unificada de sessao — mode (manual/autonomous), output (compact/verbose) e profile (preset dos dois). Fonte unica de verdade pra Mode/Output; `/ctxos:mode` e `/ctxos:output` continuam funcionando como Compatibility Commands. Preferencia persiste fora do repo (por projeto), sobrevive a `/clear`.
argument-hint: "mode <manual|autonomous> | output <compact|verbose> | profile <architect|reviewer|backend|frontend>"
disable-model-invocation: true
---

`$ARGUMENTS` vazio: mostrar Mode/Output/Profile correntes + origem de cada eixo (ver bloco Precedencia), parar aqui. Nao e o `/ctxos:status` completo — esse cobre PROJECT/RUNTIME tambem, este so os 3 eixos de sessao.

`$ARGUMENTS` nao bate em `mode <valor>`, `output <valor>` nem `profile <valor>`: nao adivinhar, mostrar uso.

## Precedencia

Toda leitura de Mode/Output nesta conversa segue esta ordem, da mais forte pra mais fraca:

1. Override explicito ja dado nesta conversa (`/ctxos:config mode ...` ou `/ctxos:config output ...` chamado durante o papo, inclusive via Compatibility Command).
2. Config persistido (arquivo do projeto, ver Persistencia abaixo).
3. Default (`manual` / `verbose`).

Setar via `mode`, `output` ou `profile` sempre grava write-through no arquivo persistido — nunca fica so na conversa.

## mode <manual|autonomous>

Ativa Manual ou Autonomous Mode pro resto desta conversa.

**manual** — pra cada pedido: (1) rodar o funil de `/ctxos:locate`, mostrar no(s)/raio/memoria encontrados; (2) esperar confirmacao explicita antes de tocar em qualquer arquivo; (3) implementar so o que foi confirmado; (4) apos implementar, esperar confirmacao explicita antes de rodar `/ctxos:commit`. Nunca pular uma das duas esperas. Indicado pra arquitetura, refatoracao delicada, investigacao, debugging.

**autonomous** — pra cada pedido em linguagem natural: (1) rodar o funil de `/ctxos:locate` internamente, mostrar resultado so se relevante pro usuario acompanhar; (2) implementar o pedido inteiro sem pausar entre passos internos; (3) rodar `/ctxos:commit` internamente ao fim do trabalho (ou de cada unidade logica); (4) entregar 1 resumo final no formato do Output corrente. PARAR e pedir confirmacao explicita quando o trabalho tocar doc de arquitetura (README/ADR/SPEC/CHANGELOG), config de infra/CI/CD, manifesto de dependencia (so ao adicionar/remover/rebaixar), schema de banco/migration, os proprios comandos/specs do Context OS (quando o projeto-alvo e o proprio Context-OS), ou quando locate devolver `✗ No match`.

Confirmacao em 1 linha: "Mode: Manual — locate e commit esperam sua confirmacao." ou "Mode: Autonomous — descreva o que quer, cuido do locate/commit."

## output <compact|verbose>

**compact** — resumo final so checklist enxuto: etapas concluidas + lista de arquivos tocados, sem explicar decisao/no/raio/memoria.

**verbose** — comportamento padrao: explica decisoes, nos, memoria, raio, eventos intermediarios relevantes.

Escopo estrito: nunca infla a saida de `/ctxos:locate` (teto ≤12 linhas, orcamento fixo, nao preferencia de verbosidade); nunca muda o que `/ctxos:commit` escreve em `.ctxos/`.

Confirmacao em 1 linha: "Output: Compact" ou "Output: Verbose".

## profile <architect|reviewer|backend|frontend>

Preset opinativo — seta Mode+Output de uma vez.

| Profile     | Mode       | Output   | Motivo |
|-------------|------------|----------|--------|
| `architect` | manual     | verbose  | Decisao arquitetural passo a passo, com explicacao completa. |
| `reviewer`  | manual     | compact  | Acompanha cada etapa, mas quer resumo enxuto. |
| `backend`   | autonomous | compact  | Lote de trabalho rapido, ruido minimo. |
| `frontend`  | autonomous | verbose  | Roda sozinho, mas quer narrativa de nos/decisoes tocados. |

Profiles sao presets opinativos e **expansiveis**: hoje configuram so Mode+Output; eixos futuros (idioma, estilo de patch, nivel de explicacao) podem entrar na mesma tabela sem quebrar profile existente. Nome do profile nao e sinonimo de "1 par de flags" — e rotulo de preferencia de trabalho que pode crescer.

Confirmacao em 1 linha: "Profile: Backend — Mode Autonomous, Output Compact."

## Persistencia

Arquivo: `~/.ctxos/projects/<hash>/config.json` (Windows: `%USERPROFILE%\.ctxos\projects\<hash>\config.json`). `<hash>` = 12 primeiros caracteres hex de sha256 do caminho absoluto do diretorio onde `.ctxos/` do projeto vive — mesma primitiva de fingerprint ja usada em index/locate/commit (sha256 sobre string, nunca commit/mtime).

```json
{
  "mode": "autonomous",
  "output": "compact",
  "profile": "backend"
}
```

**Leitura — lazy.** Nao dispara no inicio da conversa incondicionalmente. Dispara so na primeira vez que QUALQUER comando `/ctxos:*` que dependa de Mode/Output e efetivamente invocado nesta conversa (explicito ou modo autonomous ja ativo). Conversa que nunca toca ctxos nunca le o arquivo. Achou arquivo → semeia Mode/Output a partir dele antes de aplicar default. Nao achou → default atual (`manual`/`verbose`).

**Escrita — write-through imediato.** Toda chamada de `mode`, `output` ou `profile` grava o arquivo na hora, nao espera fim de sessao nem commit.

Nunca vai pro repo do projeto-alvo, nunca e staged/commitado, nunca vira 2a fonte de verdade de conhecimento do projeto — e preferencia pessoal, categoria diferente dos 4 planos em `.ctxos/`.

## Compatibility Commands

`/ctxos:mode`, `/ctxos:output`, `/ctxos:manual`, `/ctxos:autonomous` continuam funcionando, redirecionam pra cá — sem aviso de deprecated, sem remocao planejada.

## Guardrail

`think` nunca entra neste comando — continua eixo separado e Experimental (Axioma 3, `docs/DECISIONS.md` 2026-07-18); misturar com mode/output aqui promoveria ele de volta a eixo igual. Nunca toca mecanica estrutural de `/ctxos:locate` (funil, fingerprint, staleness-repair) nem `/ctxos:commit` (write-through, split, deriva semantica). Nunca introduz pausa alem da que `mode` ja define. Nenhuma resposta abre com bloco de estado — pra consultar Mode/Output/Profile correntes junto com PROJECT/RUNTIME, `/ctxos:status`.
