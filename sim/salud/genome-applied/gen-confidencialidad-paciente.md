---
id: gen-confidencialidad-paciente
trigger: la fuente contiene datos de un paciente (historia clínica, episodio, identificador)
status: active
version: 1
target_tier: semantic
seeded_by: ONBOARD (sim/salud/company.yaml)
---

Ningún dato de paciente vive como conocimiento abierto en `semantic/`. Toda página
derivada de una historia clínica va a `wiki/semantic/pacientes/` con `confidential: true`,
`pii: anonimizado` y un **ID interno seudonimizado** (nunca nombre, documento ni contacto
reales). Estas páginas NO se promueven de tier ni se comparten fuera de su contexto:
[[gen-consolidate]] debe respetar `confidential: true` y no fusionarlas con conocimiento
abierto.

El conocimiento clínico **generalizable** (qué patología, qué tratamiento estándar) se
**separa** a su [[ficha de patología]] / [[protocolo]] correspondiente. La página de
paciente solo enlaza, con relaciones tipadas, `presenta_patologia` (→ patología),
`tratado_con` (→ fármaco) y `segun_protocolo` (→ protocolo): es un nodo de episodio, no
una fuente de verdad clínica.

Si la fuente trae **PII real** (nombre completo, documento, teléfono, dirección), INGEST
se **detiene** y exige anonimización antes de continuar: la PII nunca entra a `wiki/`.
