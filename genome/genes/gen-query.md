---
id: gen-query
trigger: operación QUERY sobre un tema
status: active
version: 2
---

QUERY responde navegando el grafo, no leyendo todo. Empieza en `index.md`, sigue las
relaciones `[[...]]` relevantes y abre solo las páginas necesarias (presupuesto de contexto).
Cita las páginas-fuente consultadas y su `confidence`, **excepto las `sensibilidad: confidencial`**
([[gen-confidencialidad]]): de esas no revela contenido sensible ni las cita textualmente —
responde con referencia indirecta o ID seudonimizado. Advierte **siempre** lo vencido por
`valido_hasta` ([[gen-vigencia-temporal]]), lo contradictorio (`relations.contradice`) y la baja
`confidence`, en vez de afirmar con falsa seguridad. Si no hay información, dilo: no inventes.
