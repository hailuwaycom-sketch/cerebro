# README — Dataset ENPH 2022 (subconjunto GIEA)

**Fuente original:** DANE — Encuesta Nacional de Presupuestos de los Hogares 2022
**Responsable GIEA:** Sofía Paredes (asistente de datos)
**Fecha de descarga:** 2023-11-08
**Última actualización interna:** 2024-03-15

## Descripción

Subconjunto del módulo de servicios públicos domiciliarios de la ENPH 2022.
Contiene gasto mensual declarado en energía eléctrica, acueducto, gas natural
e internet para ~28.000 hogares en 23 departamentos.

## Variables incluidas

- `id_hogar`: identificador anonimizado del hogar
- `depto`: código DANE del departamento
- `estrato`: estrato socioeconómico 1-6
- `gasto_energia_mes`: gasto mensual declarado en energía (COP corrientes 2022)
- `kwh_mes`: consumo mensual en kWh (autoreportado)
- `tarifa_efectiva`: calculada como gasto/consumo (COP/kWh)

## Notas de calidad

- Estratos 1 y 2 presentan alta tasa de no respuesta en `kwh_mes` (~38%).
- Se detectaron valores atípicos en `tarifa_efectiva` para 214 hogares del
  Chocó (posiblemente errores de codificación); se excluyeron en WP-2024-07.
- El módulo de servicios NO distingue entre tarifa regulada y consumo subsidiado
  para estrato 1: esta limitación es conocida y fue reportada en WP-2024-07.

## Uso interno

Usado en: WP-2024-07, GIEA-2025-03 (en proceso de actualización).
No distribuir fuera del grupo sin autorización del PI (Valentina Ríos).
