---
title: Síntesis — defecto de portafiltro en Cafetera Aurora
type: sintesis
tier: semantic
tags: [sintesis, calidad, defecto-producto, cafetera-aurora, alerta-proveedor]
confidence: 0.9
created: 2026-06-22
last_reinforced: 2026-06-22
decay_rate: low          # la síntesis es conocimiento estable, aunque agregue eventos high
sources:
  - "[[raw/tickets-soporte-2026-06]]"
  - "[[raw/resenas-cafetera-aurora]]"
  - "[[raw/proveedor-electrohogar-import]]"
relations:
  usa: []
  depende_de: []
  contradice: []
  reemplaza: []
  agrega: ["[[TKT-5521]]", "[[TKT-5530]]", "[[TKT-5544]]", "[[TKT-5561]]"]
  sobre_sku: ["[[SKU-CAF-2200]]", "[[SKU-CAF-2201]]"]
  apunta_a: ["[[prov-electrohogar-import]]"]
  respalda_evolve: ["[[findings]]"]
---

# Síntesis: defecto de portafiltro — familia Cafetera Aurora

Patrón detectado por [[gen-sintesis-tickets]] al superar el umbral de 3+ tickets sobre el
mismo motivo y familia de SKU.

## Patrón
- **Motivo dominante:** portafiltro flojo / no sella al presurizar.
- **Conteo:** 4 tickets (TKT-5521, 5530, 5544, 5561) + 2 reseñas 2/5 en [[resenas-aurora]].
- **SKUs afectados:** [[SKU-CAF-2200]] (plata) y [[SKU-CAF-2201]] (negro) → el defecto NO
  es de color, es de la **familia / lote del proveedor**.
- **Proveedor:** [[prov-electrohogar-import]] (notificado 2026-06-15; un ticket aún abierto).

## Lectura
Defecto **sistémico**, no incidente aislado. Señal suficiente para escalar a compras y,
en el genoma, para proponer una regla de alerta de calidad por proveedor → ver [[findings]].
