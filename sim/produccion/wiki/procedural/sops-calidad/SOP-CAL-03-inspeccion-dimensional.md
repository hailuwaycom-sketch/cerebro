---
title: SOP-CAL-03 — Inspección dimensional y verificación de primera pieza
type: sop
tier: procedural
tags: [sop, calidad, inspeccion]
confidence: 0.95
created: 2026-06-22
last_reinforced: 2026-06-22
decay_rate: low
sources:
  - "[[raw/NCR-2026-031-calidad]]"
relations:
  usa: []
  depende_de: []
  contradice: []
  reemplaza: []
  aplica_a:
    - "[[soporte-motor-SM45]]"
  violado_por:
    - "[[NCR-2026-031]]"
---

# SOP-CAL-03 — Inspección dimensional

Proceso estable (decay bajo) de control dimensional en líneas de estampado.

## Pasos clave
1. Recibir la OP y la ficha técnica del SKU (cotas y tolerancias).
2. Calibrar instrumentos de medición contra galga patrón.
3. Medir muestra del primer lote según plan de muestreo.
4. **Verificación de primera pieza:** medir la pieza inicial del turno ANTES de liberar
   producción; registrar Cpk del subgrupo.
5. Si Cpk < 1,33 → detener, ajustar proceso, no liberar.

## Incidencias
- [[NCR-2026-031]] incumplió el paso 4 (no se verificó la primera pieza), permitiendo que
  200 piezas salieran fuera de tolerancia.
