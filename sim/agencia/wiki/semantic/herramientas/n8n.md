---
title: n8n
type: entidad
tier: semantic
tags: [herramienta, orquestacion, automatizacion]
confidence: 0.95
created: 2026-06-22
last_reinforced: 2026-06-22
decay_rate: low
sources:
  - "[[raw/2026-05-12-call-inmobiliaria-vertice]]"
  - "[[raw/2026-05-15-propuesta-inmobiliaria-vertice]]"
relations:
  usa: []
  depende_de: []
  contradice: []
  reemplaza: []
  usado_por: ["[[bot-captacion-leads]]", "[[propuesta-vertice-PROP-2026-038]]"]
---

# n8n

Plataforma de orquestación de flujos (workflow automation) que FlujoLab usa como motor para
conectar [[WhatsApp Cloud API]], calificación y agendado. Es el orquestador propuesto para
[[lead-inmobiliaria-vertice]]. Alternativa a [[Make]] según el proyecto.
