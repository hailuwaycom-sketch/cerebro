---
id: gen-migracion-genoma
trigger: tras aplicar una mutación de genoma (gen nuevo, editado o deprecado)
status: active
version: 1
---

Cambiar el genoma crea una **deuda de migración**: las páginas y el manifiesto existentes
pueden quedar desincronizados con las reglas nuevas. Tras toda mutación
([[gen-compuerta-mutacion]]), LINT corre un **pase de migración** que re-valida contra el
genoma nuevo: (1) que `onboard/company.yaml` declare lo que los genes ahora exigen
(`relation_types`, `source_trust`, `sintesis_umbral`, etc.); (2) que las páginas existentes
cumplan los campos/relaciones nuevos (p. ej. `clase`, `valido_hasta`, verbos declarados).
Reporta lo desincronizado como hallazgos de migración y **PROPONE** los arreglos (no los
aplica solo). Así, una mejora del genoma nunca deja el conocimiento previo en estado inválido
en silencio. Complementa [[gen-lint]].
