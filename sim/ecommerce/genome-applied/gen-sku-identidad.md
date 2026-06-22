---
id: gen-sku-identidad
trigger: la fuente menciona un producto del catálogo (ficha, ticket o reseña)
status: active
version: 1
seeded_by: onboard
source_manifest: sim/ecommerce/company.yaml
target_tier: semantic
---

El SKU es el identificador ESTABLE y único de cada producto: existe UNA sola página por SKU
en `wiki/semantic/productos/<SKU>.md`, sin importar cuántas fuentes lo mencionen. Los campos
volátiles (precio, stock, estado) viven en esa misma página y se ACTUALIZAN in situ (sube
`last_reinforced`), nunca se duplican. Toda otra entidad (ticket, reseña, lote de inventario)
enlaza al producto vía relación tipada `sobre_sku`, jamás recreando la ficha. Productos casi
idénticos (mismo modelo, distinto color/talla) son páginas separadas por SKU pero comparten
la relación `variante_de`. Esto evita la explosión de duplicados típica de catálogos con
miles de SKUs casi iguales.
