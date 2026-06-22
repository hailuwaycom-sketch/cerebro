---
id: gen-estado-maquina
trigger: la fuente reporta estado, mantenimiento o avería de una máquina
status: active
version: 1
target_tier: semantic
seeded_by: ONBOARD (sim/produccion/company.yaml)
---

Cada máquina es una **entidad estable con estado** en `wiki/semantic/maquinas/`, con los
campos de frontmatter `estado` (`operativa | en-mantenimiento | fuera-de-servicio`) y
`ultimo_mantenimiento`. El estado se **ACTUALIZA en la misma página** (idempotencia: no se
crea una página nueva por cada cambio de estado), subiendo `last_reinforced`. El historial
de eventos fechados (cada intervención) vive en hojas de mantenimiento separadas que
enlazan `mantiene_a` la máquina con `decay_rate: high`. Así la máquina conserva su verdad
actual (estable) y el log de intervenciones decae como evento.
