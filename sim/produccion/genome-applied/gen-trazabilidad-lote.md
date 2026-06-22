---
id: gen-trazabilidad-lote
trigger: la fuente menciona un lote, OP o número de serie de producción
status: active
version: 1
target_tier: semantic
seeded_by: ONBOARD (sim/produccion/company.yaml)
---

Todo lote es una **entidad propia** en `wiki/semantic/lotes/` y DEBE enlazar, con
relaciones tipadas, su cadena de trazabilidad completa: `producido_en` (máquina),
`bajo_orden` (OP), `de_producto` (SKU) y, si aplica, `insumo_de` (proveedor/material).
Ninguna página de lote se acepta sin al menos `producido_en` + `bajo_orden`. La
trazabilidad es bidireccional: la máquina, la OP y el producto enlazan de vuelta al lote.
Esto permite responder "qué lotes pasaron por la máquina X" o "qué OP originó el lote Y"
navegando el grafo sin leer todas las fuentes.
