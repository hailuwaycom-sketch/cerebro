# Caso de éxito — Clínica Dental Sonrisa
Documento: CASE-2026-007
Fecha: 2026-04-30
Cliente: Dental Sonrisa (firmó 2026-02-10, go-live 2026-03-03)
Origen: era lead desde 2026-01-22, convertido a cliente con propuesta PROP-2026-019.
Tipo: caso-exito / cliente

---

## Contexto
Dental Sonrisa es una clínica con 2 sucursales. Llegó como lead en enero 2026 pidiendo "algo para
no perder pacientes que escriben por WhatsApp y nunca agendan". Mismo patrón que Inmobiliaria Vértice:
fuga de leads por respuesta lenta.

Durante la call de descubrimiento pusieron la misma objeción que vemos seguido: "ya probamos un bot y
los pacientes lo odiaban". Se resolvió mostrando el motor de lenguaje natural con OpenAI y la salida a
humano.

## Solución entregada
- bot-captacion-leads + automatizacion-follow-up.
- WhatsApp Cloud API + Twilio (recordatorios SMS de cita) orquestado en Make.
- Calificación: tratamiento de interés, urgencia, sucursal. Agenda en su sistema de citas.
- Follow-up automático a las 24h y 72h a quien no agendó.

## Resultados (primeros 45 días post go-live)
- Tiempo de primera respuesta: de ~6 horas a < 1 minuto.
- Tasa de agendamiento de leads de WhatsApp: de 18% a 41%.
- No-shows reducidos 30% gracias a recordatorios por Twilio.
- ROI: recuperaron el setup en 38 días.

## Stack
- Make (orquestación), WhatsApp Cloud API, Twilio (SMS), OpenAI (NLU).

## Cita del cliente
"Pasamos de perder pacientes cada noche a llenar la agenda solos. El bot no se siente robot." 
— Dra. Patricia Ruiz, Dental Sonrisa.

## Datos comerciales (internos)
- Setup cobrado: USD 2,200 · MRR: USD 450/mes.
- Propuesta origen: PROP-2026-019. Lead origen desde 2026-01-22.
- Usar este caso como prueba social para leads del sector salud y servicios con citas.
