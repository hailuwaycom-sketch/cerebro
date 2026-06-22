---
id: gen-query
trigger: operación QUERY sobre un tema
status: active
version: 1
---

QUERY responde navegando el grafo, no leyendo todo. Empieza en `index.md`, sigue las
relaciones `[[...]]` relevantes y abre solo las páginas necesarias (presupuesto de
contexto). Responde citando siempre las páginas-fuente consultadas y su `confidence`; si
hay contradicciones (`relations.contradice`) o conocimiento vencido por `decay_rate`,
adviértelo en vez de afirmar con falsa seguridad. Si no hay información, dilo: no inventes.
