---
id: gen-evolve
trigger: operación EVOLVE / patrón repetido detectado
status: active
version: 1
---

EVOLVE es el motor mutagénico. Observa patrones repetidos en las operaciones (clasificaciones
corregidas a mano, reglas implícitas que aplicas seguido, fricciones recurrentes) y PROPONE
una mutación del genoma: crear un gen nuevo, editar uno existente (subiendo `version`) o
deprecarlo. Siempre en **modo propuesta**: muestra señal + diff y pasa por
[[gen-compuerta-mutacion]]. Nunca aplica solo. Cada mutación aprobada = 1 línea en
`genome/events.jsonl` + 1 commit + re-sync de `AGENTS.md`. Esto es lo que hace al cerebro
reproducible: el porqué de cada cambio queda auditado.
