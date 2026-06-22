---
id: gen-precio-perecedero
trigger: la fuente contiene un precio cerrado o tarifa (setup/MRR)
status: active
version: 1
seeded_by: onboard
target_tier: semantic
---

Toda página que registre un precio cerrado (setup, MRR, tarifa) nace en
`wiki/semantic/precios/` con `decay_rate: high` y un campo `valido_hasta` (por defecto
`created` + 30 días, o la fecha que diga la fuente). Pasado `valido_hasta`, [[gen-query]]
trata la página como **vencida**: advierte antes de citar el precio y nunca lo afirma como
vigente sin su fecha de vigencia. Si una propuesta nueva recotiza, la página vieja recibe
`reemplaza` desde la nueva y baja su `confidence`; no se borra (queda el histórico). Nunca
se cita un precio sin acompañarlo de su `valido_hasta`.
