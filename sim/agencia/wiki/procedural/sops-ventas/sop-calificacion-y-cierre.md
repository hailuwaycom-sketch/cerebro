---
title: SOP — Calificación y cierre de leads
type: sop
tier: procedural
tags: [sop, ventas, calificacion, cierre]
confidence: 0.85
created: 2026-06-22
last_reinforced: 2026-06-22
decay_rate: medium
sources:
  - "[[raw/2026-05-12-call-inmobiliaria-vertice]]"
  - "[[raw/2026-05-20-objeciones-playbook]]"
relations:
  usa: ["[[objecion-bot-malo]]", "[[objecion-precio-alto]]", "[[bot-captacion-leads]]"]
  depende_de: []
  contradice: []
  reemplaza: []
---

# SOP — Calificación y cierre de leads (FlujoLab)

1. **Call de descubrimiento**: identificar dolor real (casi siempre SLA de primera respuesta +
   calificación). Confirmar volumen mensual de consultas para dimensionar el tier de MRR.
2. **Manejo de objeciones** (usar páginas transversales):
   - [[objecion-bot-malo]] → mostrar [[OpenAI]] + salida a humano + [[caso-exito-dental-sonrisa]].
   - [[objecion-precio-alto]] → anclar vs. competencia + reencuadre a ROI.
3. **Propuesta**: emitir con precios y **fecha de vigencia de 30 días** ([[gen-precio-perecedero]]).
4. **Follow-up**: agendar follow-up con **fecha concreta** antes de la junta de decisión del lead.
5. **Cierre y conversión**: al firmar, convertir el lead a cliente con trazabilidad
   ([[gen-lead-a-cliente]]) — sin borrar el lead.

> Las objeciones de alta frecuencia (OBJ-01, OBJ-02) viven como conocimiento transversal y se
> refuerzan en cada call ([[gen-objecion-transversal]]).
