---
title: "Dataset — ENPH 2022 (subconjunto GIEA)"
type: dataset
tier: semantic
tags: [enph, dane, hogares, energia, dataset]
confidence: 0.85
created: 2026-06-25
last_reinforced: 2026-06-25
decay_rate: low
sources:
  - "[[raw/enph-2022-readme]]"
relations:
  cita:
    - "[[dane-fuente-oficial]]"
  supersede: []
  revisado_por: []
  deriva_de: []
  replica: []
---

# Dataset — ENPH 2022 (subconjunto GIEA)

Subconjunto del módulo de servicios públicos de la ENPH 2022 (DANE), preparado
por [[sofia-paredes]]. Cubre ~28.000 hogares en 23 departamentos; incluye
variables de gasto y consumo de energía eléctrica, acueducto, gas e internet.

Usado en [[wp-2024-07]] y [[giea-2025-03]]. Descarga original: 2023-11-08.
Nota: estratos 1 y 2 presentan alta tasa de no respuesta en `kwh_mes` (~38%).
