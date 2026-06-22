---
id: gen-lead-a-cliente
trigger: un lead existente firma una propuesta y se convierte en cliente
status: active
version: 1
seeded_by: onboard
target_tier: semantic
---

Cuando un lead firma y pasa a cliente, **no se borra ni se renombra** su página: el lead
queda en `wiki/semantic/leads/` con `status: convertido` y una relación `reemplaza_por`
hacia la nueva página de `wiki/semantic/clientes/`. La página de cliente lleva
`proviene_de` hacia el lead. Todo el historial del lead (calls, objeciones encontradas,
propuesta origen) se conserva y se enlaza desde el cliente con `[[wiki-links]]`, para no
perder el contexto de cómo se ganó la cuenta. La conversión es un cambio de estado con
trazabilidad, no una sobrescritura.
