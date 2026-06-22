---
id: gen-compuerta-mutacion
trigger: cualquier cambio dentro de genome/
status: active
version: 1
---

Ninguna mutación del genoma (crear, editar o deprecar un gen o cápsula) se aplica sola.
Flujo obligatorio en modo híbrido: (1) PROPÓN el cambio mostrando el diff y la señal que
lo motiva; (2) espera mi aprobación explícita; (3) solo entonces aplica el cambio,
incrementa `version` del gen, añade una línea a `genome/events.jsonl` con
`approved_by:"user"` y haz un commit de git; (4) re-sincroniza `AGENTS.md` con `CLAUDE.md`.
Editar manualmente este gen también requiere registrar el evento.
