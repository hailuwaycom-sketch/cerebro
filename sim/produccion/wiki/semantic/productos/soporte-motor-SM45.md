---
title: Soporte de motor SM45
type: entidad
tier: semantic
tags: [producto, sku, estampado]
confidence: 0.9
created: 2026-06-22
last_reinforced: 2026-06-22
decay_rate: low
sources:
  - "[[raw/OP-2026-0417]]"
  - "[[raw/NCR-2026-031-calidad]]"
relations:
  usa:
    - "[[AceroNorte]]"        # material acero CRS-1.5mm
  depende_de:
    - "[[PR-200-prensa]]"     # se estampa en esta prensa
  contradice: []
  reemplaza: []
  fabricado_como:
    - "[[LOTE-SM45-2606]]"
---

# Soporte de motor SM45

Producto (SKU **SM45**) estampado en chapa de acero laminado en frío CRS de 1,5 mm.
Cota crítica: **altura 45,0 mm ± 0,2**. Se fabrica en [[L1-estampado]] sobre la prensa
[[PR-200-prensa]] a partir de material de [[AceroNorte]].

## Especificación estable
- Material: CRS-1.5mm (acero laminado en frío)
- Cota crítica: altura 45,0 mm, tolerancia ±0,2 mm
- Objetivo de capacidad: Cpk ≥ 1,33

## Trazabilidad
- Lote más reciente: [[LOTE-SM45-2606]] (OP [[OP-2026-0417]]).
