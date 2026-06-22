---
id: cap-ingesta-de-fuente
status: active
version: 1
composes: [gen-raw-inmutable, gen-frontmatter-obligatorio, gen-ingest]
---

# Cápsula: ingesta de fuente

Workflow completo para convertir una fuente cruda en conocimiento enlazado.
Combina genes; ejecútalos en orden y de forma idempotente.

## Pasos
1. **Leer** la fuente desde `raw/` sin modificarla ([[gen-raw-inmutable]]).
2. **Clasificar** el tipo (`concepto | entidad | fuente | sintesis | sop`) y el `tier`
   destino (normalmente `semantic/` para hechos, `procedural/` para procesos).
3. **Crear/actualizar** la página con frontmatter válido ([[gen-frontmatter-obligatorio]]).
   Si ya existe, actualízala y sube `last_reinforced` en vez de duplicar.
4. **Extraer** conceptos y entidades clave; crea sus páginas si no existen.
5. **Enlazar** con `relations` tipadas (`usa`, `depende_de`, `contradice`, `reemplaza`)
   y `[[wiki-links]]` hacia páginas relacionadas.
6. **Registrar**: añade ancla en `index.md` si es relevante y una línea en `log.md`.

## Criterio de hecho
- La fuente quedó intacta en `raw/`.
- La nueva página tiene frontmatter, fuentes y al menos una relación entrante o saliente.
- No se duplicó conocimiento existente.
