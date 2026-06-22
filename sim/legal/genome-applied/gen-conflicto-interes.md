---
id: gen-conflicto-interes
trigger: se ingiere un cliente o caso nuevo
status: active
version: 1
seeded_from: company.yaml
---

Al ingerir un cliente, contraparte o caso nuevo, cruza la entidad contra
`entities.clientes` y `entities.contrapartes` ya registradas. Si una contraparte
coincide (mismo nombre o grupo) con un cliente actual del bufete —o un cliente
nuevo es contraparte de un caso vivo— marca `relations.contradice` entre ambas
páginas y `tag: conflicto-interes`. En `QUERY` sobre el caso o el cliente, eleva
SIEMPRE la alerta de conflicto antes de responder, para que el socio decida si
acepta el encargo o se abstiene. El conflicto de interés bloquea el flujo normal:
es una condición de parada, no un dato más.
