---
title: "Dataset — ENPH 2022 v2 (subconjunto GIEA, corrección estratos)"
type: dataset
tier: semantic
tags: [enph, dane, hogares, energia, dataset, correccion]
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
  deriva_de:
    - "[[enph-2022-giea]]"
  replica: []
---

# Dataset — ENPH 2022 v2 (subconjunto GIEA, corrección estratos)

Subconjunto revisado de [[enph-2022-giea]] generado por [[sofia-paredes]] en
marzo 2024. Corrige la codificación de 214 hogares del Chocó con valores atípicos
en `tarifa_efectiva` y excluye explícitamente el estrato 1 de la muestra analítica.
Cubre ~26.800 hogares. Usado en la versión final [[giea-2025-03]].

Deriva de [[enph-2022-giea]]; misma fuente DANE, misma descarga de noviembre 2023.
