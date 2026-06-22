---
id: gen-bulk-ingest
trigger: operación BULK INGEST
status: active
version: 1
---

BULK INGEST procesa todas las fuentes pendientes de `raw/` aplicando [[gen-ingest]] a cada
una, **una por una** (no en lote ciego), para preservar la calidad de clasificación y
enlazado. Lleva la cuenta de procesadas vs. omitidas, evita reprocesar lo ya ingerido
(idempotencia) y, al final, actualiza `index.md` y deja un resumen en `log.md` con totales
y cualquier fuente que requirió decisión humana.
