---
id: gen-lint
trigger: operación LINT (mantenimiento)
status: active
version: 1
---

LINT mantiene sano el grafo. Detecta: (a) páginas huérfanas (sin relaciones entrantes ni
salientes, exceptuando `type: meta`); (b) contradicciones entre páginas; (c) páginas
vencidas según `last_reinforced` + `decay_rate`. Para cada hallazgo PROPÓN una acción
(conectar, fusionar, marcar `contradice`, bajar `confidence` o deprecar) y aplícala solo
tras mi aprobación. No modifica el genoma por sí mismo; si detecta un patrón de regla,
deriva a [[gen-evolve]]. Deja constancia en `log.md`.
