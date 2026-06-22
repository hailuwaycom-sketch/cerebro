---
id: gen-dato-volatil
trigger: la fuente trae precio, stock/inventario o disponibilidad de un SKU
status: active
version: 1
seeded_by: onboard
source_manifest: sim/ecommerce/company.yaml
target_tier: semantic
---

Precio e inventario son datos VOLÁTILES. En la ficha del producto se marcan con su propio
`decay_rate: high` y un campo `valido_a: <fecha>`, separados de los specs estables (material,
dimensiones, categoría) que llevan `decay_rate: low`. Por eso una misma ficha de producto
puede sostener `confidence` alta en sus specs y, a la vez, precio/stock vencidos. Al
consultar (QUERY), si `valido_a` es anterior a hoy, el agente lo ADVIERTE y no afirma el
número como vigente. Nunca se borra el dato viejo: se registra como reemplazado
(`relations.reemplaza`) por el nuevo valor. Implica que el `decay_rate` no es propiedad de la
página entera sino que puede convivir a nivel de campo dentro de una entidad estable.
