---
id: gen-trazabilidad-clinica
trigger: la fuente relaciona un fármaco, una patología y/o un protocolo de tratamiento
status: active
version: 1
target_tier: semantic
seeded_by: ONBOARD (sim/salud/company.yaml)
---

Mantén trazable la **tríada clínica** fármaco ↔ patología ↔ protocolo con relaciones
tipadas y bidireccionales:
- el protocolo `indica` el fármaco y `trata` la patología;
- el fármaco es `tratamiento_de` la patología;
- la patología es `tratada_segun` el protocolo.

Toda ficha de fármaco enlaza **al menos** una patología o un protocolo; ninguna queda
huérfana (lo verifica [[gen-lint]]). Igual para patologías: toda patología activa enlaza su
protocolo de manejo si existe.

Esto permite responder, navegando el grafo sin abrir todas las fuentes, preguntas como
"¿qué fármaco indica el protocolo de anafilaxia?" o "¿qué protocolos usan adrenalina y a
qué dosis?". La dosis, vía e indicación viven en la ficha de fármaco y/o el protocolo, no
en la página de paciente (ver [[gen-confidencialidad-paciente]]).
