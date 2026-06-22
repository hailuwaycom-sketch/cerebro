---
id: gen-sintesis-tickets
trigger: se han ingerido 3+ tickets de soporte sobre el mismo SKU o mismo motivo
status: active
version: 1
seeded_by: onboard
source_manifest: sim/ecommerce/company.yaml
target_tier: semantic
---

Los tickets individuales son episódicos (`decay_rate: high`) y viven en
`wiki/semantic/tickets/`. Cuando 3+ tickets comparten SKU o causa raíz, se crea/actualiza una
página de SÍNTESIS en `wiki/semantic/sintesis/` (`type: sintesis`) que agrega el patrón
(motivo dominante, conteo, SKU afectado) y enlaza `agrega` a cada ticket fuente y `sobre_sku`
al producto. La síntesis SÍ es conocimiento estable (`decay_rate: low`): convierte ruido
episódico de alto volumen en una señal consultable y duradera. Si la síntesis revela un
defecto sistémico de un producto o proveedor, dispara [[gen-evolve]] en modo propuesta (p.ej.
sembrar un gen de alerta de calidad por proveedor).
