**Experimental — fora do escopo da v1.** Congelado ate `docs/FIELD-TEST.md`
confirmar a hipotese central. Nao remover, nao evoluir, nao discutir.

# Adapters opcionais (documentacao — nunca dependencia)

ctxos funciona 100% so com leitura de arquivo + git opcional. Ferramentas de
analise estatica (tree-sitter, repo-maps, graphify e afins) sao ACELERADORES
detectados em runtime, nunca requisito de instalacao. Ausentes → leitura
direta dos arquivos, mesmo resultado, mais lento.

## Onde cada adapter encaixa

| Ferramenta | Acelera | Se ausente |
|---|---|---|
| `tree-sitter` (ou LSP local) | Extrair simbolos/entry-points reais em vez de inferir por leitura — passo 3 do `index` e passo 3 do `commit` (novo no) ficam mais precisos. | Index le o arquivo inteiro e infere `entradas:`/`arquivos:` por leitura direta — mesmo campo final, mais tokens gastos. |
| `repo-maps` / grafo de import estatico | Passo 6 do `index` (arestas do `graph.md`) — arestas de import viram deterministicas em vez de inferidas por grep. | `graph.md` continua sendo escrito, so que a partir de grep/leitura, sujeito a mais julgamento do agente. |
| `graphify` (ou ferramenta equivalente de grafo de conhecimento) | Pode alimentar a expansao de raio do `locate` (passo 5) com aresta ja calculada em vez de reler `graph.md` linha a linha. | `locate` le `graph.md` normalmente — arquivo markdown plano, sem indexacao externa necessaria. |

## Regra dura

Nenhum dos 4 planos pode ter um campo que so faz sentido com um adapter
instalado. Se amanha nenhuma dessas ferramentas existir, os arquivos em
`.ctxos/` continuam legiveis e completos — e o proprio teste acido de
portabilidade do README. Adapter detectado e usado silenciosamente pra
acelerar a ESCRITA do plano; nunca muda o FORMATO do plano.

## Deteccao

Sem configuracao dedicada: `index`/`commit` checam se o binario/servidor do
adapter esta disponivel no ambiente (ex: `tree-sitter` no PATH, servidor
`graphify` respondendo) antes de usar; timeout curto, fallback silencioso
pra leitura direta se nao responder.
