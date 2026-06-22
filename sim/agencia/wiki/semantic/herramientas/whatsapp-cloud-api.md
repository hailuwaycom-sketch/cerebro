---
title: WhatsApp Cloud API
type: entidad
tier: semantic
tags: [herramienta, canal, mensajeria]
confidence: 0.95
created: 2026-06-22
last_reinforced: 2026-06-22
decay_rate: low
sources:
  - "[[raw/2026-05-12-call-inmobiliaria-vertice]]"
  - "[[raw/2026-05-15-propuesta-inmobiliaria-vertice]]"
  - "[[raw/2026-04-30-caso-exito-dental-sonrisa]]"
relations:
  usa: []
  depende_de: []
  contradice: []
  reemplaza: []
  usado_por: ["[[bot-captacion-leads]]", "[[cliente-dental-sonrisa]]"]
---

# WhatsApp Cloud API

Canal principal de captación de leads de FlujoLab. Permite respuesta < 30s, 24/7, cumpliendo
el SLA de primera respuesta. Es el canal del producto [[bot-captacion-leads]]. Se combina con
[[Twilio]] cuando hace falta SMS, y con [[OpenAI]] para comprensión de lenguaje natural.
