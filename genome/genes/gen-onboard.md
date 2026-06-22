---
id: gen-onboard
trigger: operación ONBOARD (primera vez o cambio de empresa)
status: active
version: 1
---

ONBOARD adapta el cerebro genérico a una empresa concreta. Entrevista al usuario sobre:
sector/industria, tipos de documento que maneja, entidades clave, glosario y siglas
internas, roles del equipo y quién aprueba mutaciones, e idioma. Con eso: (1) completa
`genome/company-profile.md` y pon `status: configurado`; (2) PROPÓN genes específicos del
sector (taxonomía de carpetas, reglas de clasificación, glosario) pasando por
[[gen-compuerta-mutacion]]; (3) actualiza `index.md` con la empresa y las anclas iniciales.
No ingieras contenido todavía: ONBOARD solo configura.
