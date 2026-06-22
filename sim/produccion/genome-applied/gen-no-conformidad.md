---
id: gen-no-conformidad
trigger: la fuente es un registro de calidad con un defecto, rechazo o desviación (NCR)
status: active
version: 1
target_tier: semantic
seeded_by: ONBOARD (sim/produccion/company.yaml)
---

Toda NCR (no-conformidad) va a `wiki/semantic/calidad/` con `severidad`
(`menor | mayor | critica`), `lote` afectado y `disposicion`
(`reproceso | rechazo | concesion`). DEBE enlazar `afecta_a` (el lote) y, si la causa
es de proceso, `mitiga_con` o `viola` (el SOP de calidad aplicable). Una NCR es un
**evento fechado**, no conocimiento estable: nace con `decay_rate: high` y `confidence`
no se refuerza por el paso del tiempo. El conocimiento estable (la causa raíz recurrente,
la regla de prevención) se promueve aparte vía CONSOLIDATE, no se deja en la NCR.
