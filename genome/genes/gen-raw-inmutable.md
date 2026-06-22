---
id: gen-raw-inmutable
trigger: cualquier acceso a raw/
status: active
version: 1
---

`raw/` es solo-lectura. Nunca edites, renombres ni borres una fuente en `raw/`: es la
verdad inmutable del sistema. Todo conocimiento derivado vive en `wiki/` y referencia su
fuente con `sources: [[raw/...]]`. Si una fuente está desactualizada, no la toques: crea
una fuente nueva y marca la relación `reemplaza` en la página de wiki correspondiente.
