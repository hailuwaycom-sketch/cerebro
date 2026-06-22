---
id: gen-vigencia-protocolo
trigger: la fuente es un protocolo clínico, guía de práctica (GPC) o SOP asistencial
status: active
version: 1
target_tier: procedural
seeded_by: ONBOARD (sim/salud/company.yaml)
---

Todo protocolo o guía de práctica clínica lleva en su frontmatter `valid_until` (fecha de
caducidad explícita) y `decay_rate: high`: en clínica un protocolo vencido es **peligroso**,
no solo viejo. Si `valid_until` < hoy (o `last_reinforced + vigencia` ya pasó), la página
se marca `status: vencido` y [[gen-query]] DEBE anteponer una **ADVERTENCIA de caducidad**
antes de citarla, nunca afirmar con confianza plena sobre un protocolo caducado.

El versionado es estricto: una versión nueva enlaza `reemplaza` (→ versión anterior) y la
anterior pasa a `status: deprecado`. **Versión vigente y vencida nunca coexisten como
activas**; [[gen-lint]] señala cualquier protocolo `status: vencido` aún sin reemplazo como
hallazgo de seguridad prioritario.

Aplica tanto en `wiki/procedural/protocolos-clinicos/` (la guía vigente) como a sus
referencias entrantes: una historia de paciente que invocó un protocolo ya vencido es una
señal de incidente a revisar.
