# Parking lot

Ideias que surgirem fora do escopo da fase atual nao sao implementadas.
Entram aqui, 1 frase, nada mais. Revisitar quando fizer sentido pra
fase seguinte.

- Painel de metricas de sessao (tokens/custo/tempo/comandos) estilo Ruflo — exigiria statusline real do Claude Code lendo estado escrito em disco, subsistema proprio, nao knob de comando (ver DECISIONS.md 2026-07-18).
- Metrica de token/custo/tempo dentro do proprio `/ctxos:status` — descartado nesta rodada (v0.6.1), mesma razao acima: modelo nao tem acesso confiavel a numero real.
- Pipeline adaptativo (pular `/ctxos:locate` quando confianca alta dispensa) — Research em `docs/VISION.md` (Confianca), nao vira comando sem medir primeiro via `.ctxos/runtime/judgments.log` ja existente. Diferente de confidence continuo (esse e rejeitado, `docs/DECISIONS.md` 2026-07-17 — nao esta estacionado, foi decidido nao fazer).
- Mode/Output deixarem de ser comando publico, virarem so implementacao interna de `profile` (v0.7.0 ja reordenou README pra profile-first, mas manteve os 2 eixos publicos) — precisa dado de uso real (quantos usuarios setam mode/output direto vs so trocam profile) antes de reverter o Compatibility Commands, mesmo padrao de rigor que derrubou `think`.
