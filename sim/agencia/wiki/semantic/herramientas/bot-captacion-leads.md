---
title: bot-captacion-leads
type: entidad
tier: semantic
tags: [producto, servicio, bot, captacion]
confidence: 0.95
created: 2026-06-22
last_reinforced: 2026-06-22
decay_rate: low
sources:
  - "[[raw/2026-05-12-call-inmobiliaria-vertice]]"
  - "[[raw/2026-05-15-propuesta-inmobiliaria-vertice]]"
  - "[[raw/2026-04-30-caso-exito-dental-sonrisa]]"
relations:
  usa: ["[[WhatsApp Cloud API]]", "[[OpenAI]]", "[[n8n]]"]
  depende_de: ["[[WhatsApp Cloud API]]"]
  contradice: []
  reemplaza: []
  usado_por: ["[[lead-inmobiliaria-vertice]]", "[[cliente-dental-sonrisa]]"]
---

# bot-captacion-leads

Producto estrella de FlujoLab: bot que capta y califica leads por [[WhatsApp Cloud API]] con
respuesta < 30s 24/7, motor [[OpenAI]] y orquestación en [[n8n]] o [[Make]]. Califica
(presupuesto, zona, urgencia), agenda al SQL y escala a humano. El bot capta y califica; el
cierre lo hace una persona. Validado en [[caso-exito-dental-sonrisa]] (agendamiento 18%→41%).
