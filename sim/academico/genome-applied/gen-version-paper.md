---
id: gen-version-paper
trigger: existe un working-paper y su versión publicada del mismo estudio en la wiki
status: active
version: 1
seeded_from: company.yaml
---

Cuando un paper publicado (PP) corresponde al mismo estudio que un working-paper
ya ingresado, se crea una página separada para el PP y se marca
`relations.supersede: [[id-del-wp]]`. El working-paper baja a `confidence <= 0.5`
y recibe `tag: supersedido`; nunca se borra porque el historial de estimaciones
preliminares es valioso (permite rastrear cómo cambió el resultado entre versiones).

En QUERY, si existen ambas versiones, se prioriza y cita la publicada. El WP se
menciona solo para contexto histórico o para señalar diferencias entre estimaciones
preliminares y finales.

Idempotente con [[gen-ingest]]: reingerir el mismo PP no duplica la página ni
vuelve a bajar la confidence del WP si ya fue bajada.
