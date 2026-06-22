---
title: PR-200 — Prensa hidráulica 200 t
type: entidad
tier: semantic
tags: [maquina, prensa, L1-estampado]
confidence: 0.95
created: 2026-06-22
last_reinforced: 2026-06-22
decay_rate: low
estado: operativa
ultimo_mantenimiento: 2026-06-19
proximo_preventivo: 2026-07-19
sources:
  - "[[raw/OP-2026-0417]]"
  - "[[raw/MTTO-PR-200-2026-06]]"
relations:
  usa: []
  depende_de: []
  contradice: []
  reemplaza: []
  ubicada_en:
    - "[[L1-estampado]]"
  produjo_lote:
    - "[[LOTE-SM45-2606]]"
  intervenida_por:
    - "[[MTTO-PR-200-2026-06]]"
---

# PR-200 — Prensa hidráulica 200 t

Prensa hidráulica de 200 toneladas en [[L1-estampado]]. Entidad **estable con estado**
(ver [[gen-estado-maquina]]): el campo `estado` se actualiza en esta misma página; el
historial de intervenciones vive en hojas de mantenimiento que enlazan aquí.

## Estado actual
- **estado:** operativa
- **ultimo_mantenimiento:** 2026-06-19 (correctivo + recalibración de tope de troquel)
- **proximo_preventivo:** 2026-07-19

## Historial de intervenciones
- [[MTTO-PR-200-2026-06]] — 2026-06-19, reemplazo de inserto de troquel TRQ-SM45-A y
  recalibración del tope de altura (raíz de [[NCR-2026-031]]).
