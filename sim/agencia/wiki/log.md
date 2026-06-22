# log — FlujoLab (sandbox agencia)

- 2026-06-22 ONBOARD: aplicado company.yaml de FlujoLab. Sembrados 3 genes (gen-precio-perecedero, gen-lead-a-cliente, gen-objecion-transversal) en genome-applied/. Taxonomía creada.
- 2026-06-22 BULK INGEST: 4 fuentes de raw/ procesadas una por una.
  - call-inmobiliaria-vertice → lead-inmobiliaria-vertice + followup (working) + objeciones OBJ-01/OBJ-02.
  - propuesta-inmobiliaria-vertice → propuesta-vertice-PROP-2026-038 + precio-vertice (decay high, valido_hasta 2026-06-14).
  - caso-exito-dental-sonrisa → cliente-dental-sonrisa + lead-dental-sonrisa (convertido) + caso-exito-dental-sonrisa.
  - objeciones-playbook → reforzó OBJ-01 y OBJ-02 (confidence subió, multi-fuente).
  - Entidades-herramienta creadas: bot-captacion-leads, n8n, Make, WhatsApp Cloud API, Twilio, OpenAI.
- 2026-06-22 Nota: precio-vertice quedó VENCIDO a fecha de hoy (2026-06-22 > valido_hasta 2026-06-14). followup-vertice también vencido. Candidatos a LINT.
