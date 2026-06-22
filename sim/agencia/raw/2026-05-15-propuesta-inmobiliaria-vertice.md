# Propuesta comercial — Inmobiliaria Vértice
Documento: PROP-2026-038
Fecha de emisión: 2026-05-15
Válida hasta: 2026-06-14 (30 días)
Preparada por: Diego Fuentes (FlujoLab) · Para: Laura Méndez (Inmobiliaria Vértice)
Tipo: propuesta / precios-cerrados

---

## Problema
Inmobiliaria Vértice recibe ~200 consultas/mes (web + WhatsApp) y pierde ~50% de los leads por
respuesta lenta fuera de horario y por falta de calificación previa.

## Solución propuesta: bot-captacion-leads
- Canal: WhatsApp Cloud API, respuesta < 30s, 24/7 (SLA garantizado).
- Calificación automática: presupuesto, zona, compra/renta. Solo SQL pasan a agente.
- Motor de lenguaje natural con OpenAI (no árbol de botones), con escalado a humano.
- Orquestación en n8n. Agenda visitas en Google Calendar del agente.
- Fase 1: escritura a Google Sheet intermedia. Fase 2 (opcional): integración CRM legacy.

## Alcance incluido
- Diseño de flujo conversacional y 1 ronda de ajustes.
- Hasta 3 criterios de calificación.
- Dashboard-reporting básico (leads captados, SQL, visitas agendadas).
- 2 semanas de soporte post-lanzamiento.

## NO incluido (fuera de alcance)
- Integración a CRM legacy (se cotiza aparte en Fase 2).
- Campañas de pauta / generación de tráfico.

## Precios cerrados
- Setup (pago único): USD 2,400
- MRR (mantenimiento mensual): USD 480/mes
- Fase 2 integración CRM (estimado, sujeto a discovery): USD 1,200 adicionales

> Anclaje: la competencia cotizó USD 8,000 solo de setup. Nuestro setup es 70% menor.

## Tiempos
- Implementación: 3 semanas desde la firma.
- Go-live objetivo: 2026-06-09 si firman antes del 2026-05-19.

## Condiciones
- Precios válidos hasta 2026-06-14. Después se recotiza.
- 50% de setup al firmar, 50% al go-live. MRR inicia al mes del go-live.
