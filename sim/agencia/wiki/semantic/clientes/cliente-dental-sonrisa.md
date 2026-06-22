---
title: Cliente — Dental Sonrisa
type: entidad
tier: semantic
tags: [cliente, salud, activo, mantenimiento]
confidence: 0.95
created: 2026-06-22
last_reinforced: 2026-06-22
decay_rate: low
estado: activo
firmo: 2026-02-10
go_live: 2026-03-03
sources:
  - "[[raw/2026-04-30-caso-exito-dental-sonrisa]]"
relations:
  usa: ["[[bot-captacion-leads]]", "[[Make]]", "[[WhatsApp Cloud API]]", "[[Twilio]]", "[[OpenAI]]"]
  depende_de: []
  contradice: []
  reemplaza: []
  proviene_de: ["[[lead-dental-sonrisa]]"]
  generó_caso: ["[[caso-exito-dental-sonrisa]]"]
---

# Cliente — Dental Sonrisa

Clínica dental, cliente activo en mantenimiento desde go-live 2026-03-03 (firmó 2026-02-10).
Proviene del lead [[lead-dental-sonrisa]] ([[gen-lead-a-cliente]]): historial y objeciones del
lead se conservan y enlazan aquí.

- **Solución**: [[bot-captacion-leads]] + automatizacion-follow-up sobre [[Make]],
  [[WhatsApp Cloud API]], [[Twilio]] (SMS) y [[OpenAI]].
- **Comercial**: setup USD 2,200 · MRR USD 450/mes. Propuesta origen PROP-2026-019.
- **Resultado**: ver [[caso-exito-dental-sonrisa]].
