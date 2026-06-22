---
id: gen-objecion-transversal
trigger: la fuente menciona una objeción de venta y su respuesta
status: active
version: 1
seeded_by: onboard
target_tier: semantic
---

Cada objeción de venta vive como **página propia y reutilizable** en
`wiki/semantic/objeciones/` (conocimiento transversal, NO atado a un solo lead o cliente).
Las calls y casos donde aparece la enlazan con la relación `usa` hacia la página de
objeción. Cada vez que una nueva fuente confirma la misma objeción, sube su `confidence` y
su `last_reinforced` (no se duplica) y se refuerza su campo de frecuencia. Las respuestas
estándar a objeciones de alta frecuencia se promueven al SOP de ventas en
`wiki/procedural/`. Una objeción es un activo compartido del equipo, no una anécdota.
