---
id: gen-secreto-profesional
trigger: la fuente contiene info amparada por secreto profesional (minutas, estrategia, datos sensibles del cliente)
status: active
version: 1
seeded_from: company.yaml
---

Cuando una fuente contenga información amparada por secreto profesional
(minutas con cliente, estrategia procesal, márgenes de negociación, datos
sensibles), la página resultante lleva `tag: confidencial` y el campo
`secreto_profesional: true` en su frontmatter. NO se enlaza como conocimiento
abierto desde `index.md`; se restringe a la carpeta del cliente o caso
(`wiki/semantic/clientes/` o `wiki/semantic/casos/`). En `QUERY` nunca se cita
su contenido textual sensible (cifras de negociación, debilidades del cliente)
salvo petición explícita y autorizada. El secreto profesional pesa más que la
apertura del conocimiento: ante la duda, restringe.
