---
id: gen-ingest
trigger: operación INGEST sobre una fuente
status: active
version: 1
---

INGEST convierte una fuente en conocimiento enlazado siguiendo la cápsula
[[ingesta-de-fuente]]: lee desde `raw/` sin tocarla, clasifica tipo y tier, crea/actualiza
la página con frontmatter, extrae conceptos y entidades (creando sus páginas si faltan),
enlaza con relaciones tipadas y `[[wiki-links]]`, y registra ancla en `index.md` (si aplica)
+ línea en `log.md`. Idempotente: si la página ya existe, actualiza y refuerza en vez de
duplicar. No inventa datos que no estén en la fuente.
