---
id: gen-frontmatter-obligatorio
trigger: crear o actualizar una página en wiki/
status: active
version: 1
---

Toda página de `wiki/` nace y se mantiene con frontmatter YAML válido:
`title, type, tier, tags, confidence (0.0-1.0), created, last_reinforced, decay_rate,
sources, relations{usa,depende_de,contradice,reemplaza}`. Sin frontmatter no se considera
conocimiento. Al actualizar una página existente por una fuente que la confirma, sube
`last_reinforced` a hoy y ajusta `confidence`; no crees un duplicado. Las páginas meta
(`type: meta`, ej. `index.md`) quedan exentas de este gen y del LINT de huérfanos.
