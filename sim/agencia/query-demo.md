# QUERY demo — FlujoLab

## Pregunta
"Inmobiliaria Vértice nos va a confirmar esta semana. ¿En qué estado está, qué le cotizamos,
qué objeciones tiene y qué prueba social le puedo mostrar?"

## Navegación (presupuesto de contexto)
Ruta seguida desde `index.md` → sin leer toda la wiki:
1. `index.md` → sección Leads → [[lead-inmobiliaria-vertice]].
2. Desde el lead, relaciones tipadas:
   - `recibio_propuesta` → [[propuesta-vertice-PROP-2026-038]] → `define_precio` →
     [[precio-vertice-PROP-2026-038]].
   - `usa` → [[objecion-bot-malo]], [[objecion-precio-alto]].
   - `tiene_followup` → [[followup-vertice-2026-05-16]].
3. La objeción [[objecion-bot-malo]] tiene `usada_en` y prueba `prueba_para` →
   [[caso-exito-dental-sonrisa]].

(Páginas abiertas: 6 de 18. No se cargó la wiki entera.)

## Respuesta
**Estado**: [[lead-inmobiliaria-vertice]] está *en negociación* (confidence 0.85); recibió la
propuesta y su junta de decisión era el lunes 2026-05-19. Hay un follow-up
[[followup-vertice-2026-05-16]] que a hoy (2026-06-22) figura **vencido** — confirmar si la
ventana de decisión ya pasó antes de prometer "esta semana".

**Cotización**: setup USD 2,400 + MRR USD 480/mes (Fase 2 CRM ~USD 1,200), según
[[precio-vertice-PROP-2026-038]] (confidence 0.9).
⚠️ **ADVERTENCIA ([[gen-precio-perecedero]] + [[gen-query]])**: ese precio tenía
`valido_hasta: 2026-06-14`. **Está VENCIDO** a fecha de consulta (2026-06-22). No cotizarlo
como vigente sin recotizar.

**Objeciones**: dos de alta frecuencia →
- [[objecion-bot-malo]] ("ya tuvimos un bot malo"): responder con motor [[OpenAI]] + salida a
  humano.
- [[objecion-precio-alto]]: anclar vs. los USD 8,000 de la competencia + reencuadre a ROI.

**Prueba social**: [[caso-exito-dental-sonrisa]] (confidence 0.95) — mismo patrón de fuga de
leads; agendamiento 18%→41%, ROI en 38 días, "el bot no se siente robot". Sirve a la vez como
respuesta directa a [[objecion-bot-malo]].

## Fuentes citadas
- [[lead-inmobiliaria-vertice]] (0.85) · [[precio-vertice-PROP-2026-038]] (0.9, **vencido**) ·
  [[objecion-bot-malo]] (0.9) · [[objecion-precio-alto]] (0.9) ·
  [[caso-exito-dental-sonrisa]] (0.95) · [[followup-vertice-2026-05-16]] (0.8, **vencido**).
