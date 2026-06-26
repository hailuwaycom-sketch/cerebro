---
id: gen-cita-trazable
trigger: la página contiene una afirmación empírica cuantitativa (coeficiente, elasticidad, media, tasa, etc.)
status: active
version: 1
seeded_from: company.yaml
---

Toda afirmación empírica que incluya una cifra cuantitativa (coeficiente estimado,
elasticidad, media, proporción, etc.) debe llevar en `relations.cita` al menos un
enlace al dataset o paper-fuente del que se obtiene el número. Si la página fuente
aún no existe en la wiki, INGEST la crea de inmediato o deja la relación marcada
con `tag: cita-pendiente` hasta que se ingiera la fuente.

Páginas sin trazabilidad de fuente para afirmaciones empíricas parten con
`confidence <= 0.4`, independientemente del tipo de documento. La confianza solo
sube cuando la fuente es ingresada y enlazada.

Aplica tanto a páginas propias (WP, preprint) como a síntesis y páginas de
conceptos que referencien estimaciones de terceros.
