---
id: gen-version-contrato
trigger: la fuente es una versión nueva de un contrato o cláusula ya existente
status: active
version: 1
seeded_from: company.yaml
---

Cuando una fuente sea una versión nueva de un contrato o cláusula que ya tiene
página, NO se sobrescribe la anterior: se crea una página nueva para la versión
y se marca `relations.reemplaza: [[version-anterior]]`. La versión anterior baja
a `confidence <= 0.4` y recibe `tag: historico`, pero nunca se borra: el rastro
de negociación (qué cambió y por qué entre v1→v2→v3) es conocimiento jurídico
valioso. Cada versión cita su fuente en `raw/` y enlaza al contrato y al caso.
Idempotente con [[gen-ingest]]: reingerir la misma versión refuerza, no duplica.
