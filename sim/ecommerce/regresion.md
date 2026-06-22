# REGRESIÓN — E-COMMERCE / RETAIL (TiendaNube Andina) vs genoma NUEVO (16 genes)

Verifica si las 3 fricciones de [[findings]] quedan resueltas por las mutaciones del genoma
base. NO se reconstruye el escenario: se reusan las páginas ya aplicadas en `wiki/` y las
fuentes de `raw/`. Solo lectura sobre `genome/` y `raw/`; escritura solo en este sandbox.

Fecha de regresión: 2026-06-22.

---

## Fricción 1 — `decay_rate` por página vs. por campo

**Antes:** la ficha SKU mezcla specs estables (`low`) con precio/stock volátiles (`high`) en
una misma entidad. El genoma base solo tenía `decay_rate` de página; hubo que inventar una
tabla `valido_a` en el CUERPO (ver `SKU-CAF-2200.md` actual, líneas 37-44).

**Mutación a verificar:** M4 — [[gen-clase-temporal]] v1: una página `estable` puede declarar
`volatile_fields: [{campo, valido_a}]`, reusando [[gen-vigencia-temporal]] a nivel de campo.

**Re-derivación del frontmatter de SKU-CAF-2200** (fuente `raw/catalogo-2026-06`, válido a 2026-06-20):

```yaml
title: SKU-CAF-2200 — Cafetera Espresso Aurora 15 bar (Plata)
type: entidad
tier: semantic
clase: estable                 # gen-clase-temporal: conocimiento durable
decay_rate: low                # la página (specs) es estable
confidence: 0.95
volatile_fields:               # M4: campos que envejecen más rápido que la página
  - { campo: precio,  valido_a: 2026-06-20 }
  - { campo: stock,   valido_a: 2026-06-20 }
  - { campo: estado,  valido_a: 2026-06-20 }
relations:
  usa: ["[[cat-cafeteras]]"]
  depende_de: ["[[prov-electrohogar-import]]"]
```

Ahora la volatilidad por campo es **frontmatter estructurado** (no prosa en el cuerpo), una
sola página por SKU (respeta [[gen-sku-identidad]]), y QUERY puede comparar cada `valido_a`
contra hoy (2026-06-22 > 2026-06-20 ⇒ advertir precio/stock vencidos) sin partir el SKU.

**VEREDICTO: ✅ resuelto.** M4 expresa exactamente "página estable salvo estos campos que
vencen". Único matiz: el gen lista `{campo, valido_a}` sin `decay_rate` por campo; el
`decay_rate: high` implícito de un campo volátil es semántico, no declarado. No es fricción
nueva, pero conviene dejarlo escrito.

---

## Fricción 2 — vocabulario de relaciones del dominio + confianza por clase de fuente

**Antes:** `relations` era set cerrado `{usa, depende_de, contradice, reemplaza}`; `sobre_sku`,
`variante_de`, `suministra`, `agrega`, `referido_por`, `refuerza` se metieron como claves extra
fuera del contrato → LINT/QUERY no las entienden. Y la reseña se marcó `confidence: 0.4` a mano,
sin nada que obligue a degradar por clase de fuente.

**Mutaciones a verificar:** M1 (`relation_types` en company.yaml) + [[gen-frontmatter-obligatorio]]
v3 (relations = núcleo ∪ declarados); M6 [[gen-confianza-por-fuente]] + `source_trust`.

**Re-derivación del frontmatter de resenas-aurora** (fuente `raw/resenas-cafetera-aurora`):

```yaml
title: Reseñas — Cafetera Aurora (agregado)
type: fuente
clase: estable
confidence: 0.4                # debería anclarse a source_trust.blanda
tags: [resena, senal-blanda, cafetera-aurora]
relations:
  sobre_sku: ["[[SKU-CAF-2200]]", "[[SKU-CAF-2201]]"]   # verbo de dominio
  refuerza:  ["[[sintesis-portafiltro-aurora]]"]         # corrobora, no afirma
```

**Hallazgo de regresión (parcial):** el genoma base AHORA admite ambos mecanismos, PERO el
manifiesto del sandbox `sim/ecommerce/company.yaml` **no fue actualizado**: no tiene bloque
`relation_types` ni `source_trust` (el que sí los trae es `onboard/company.example.yaml`).
Consecuencias concretas:

- M1: [[gen-frontmatter-obligatorio]] v3 valida cada relación contra la **unión núcleo ∪
  declarados**. Como el sandbox no declara `sobre_sku`, `variante_de`, `variantes`,
  `referido_por`, `suministra`, `agrega`, `refuerza`, `apunta_a`, `respalda_evolve`, LINT las
  marcaría TODAS como verbos no declarados. El mecanismo existe pero la config del sandbox no lo
  alimenta ⇒ la fricción solo se cierra al añadir esos `relation_types` al manifiesto.
- M6: sin `source_trust`, la `confidence: 0.4` de la reseña sigue siendo criterio del agente,
  no un anclaje determinista `blanda → 0.4`. El gen [[gen-confianza-por-fuente]] da el contrato
  (oficial ≥0.85 / blanda ≤0.5 + entra solo como corroboración), y la reseña YA respeta el
  espíritu (0.4, relación `refuerza`, nunca afirmación única), pero la reproducibilidad depende
  del bloque ausente.

**VEREDICTO: 🟡 parcial.** El genoma nuevo da las dos capacidades y el comportamiento observado
ya las honra de facto; falta portar `relation_types` y `source_trust` al
`sim/ecommerce/company.yaml` para que M1/M6 sean deterministas y LINT no marque falsos positivos.

---

## Fricción 3 — agregación de N eventos de alto volumen (defecto sistémico invisible)

**Antes:** [[gen-consolidate]] fusionaba duplicados y movía tiers, pero NO agregaba N eventos
episódicos en una página de patrón. Sin `gen-sintesis-tickets` (sembrado), el genoma base
dejaría los tickets sueltos sin detectar el defecto.

**Mutación a verificar:** M7 — [[gen-sintesis-de-volumen]] v1 (generaliza el seed al genoma base)
+ `sintesis_umbral` (default 3).

**Re-derivación de la síntesis** (fuentes `raw/tickets-soporte-2026-06` + `raw/resenas-cafetera-aurora`):

- Eventos `clase: evento` con clave común (motivo=defecto-portafiltro, familia Aurora):
  TKT-5521 (CAF-2200), TKT-5530 (CAF-2200), TKT-5544 (CAF-2201), TKT-5561 (CAF-2200) = **4 ≥ 3**.
  (TKT-5550 es consulta-disponibilidad de SKU-OLL-0900 → clave distinta, NO entra: el umbral
  discrimina bien.)
- ⇒ CONSOLIDATE crea/actualiza `type: sintesis`, `decay_rate: low`, `agrega` los 4 tickets,
  `sobre_sku` a ambas variantes, y como el patrón es defecto sistémico **deriva a [[gen-evolve]]**
  (`respalda_evolve → findings`). Coincide con `sintesis-portafiltro-aurora.md` ya aplicada.

```yaml
type: sintesis
decay_rate: low
relations:
  agrega: ["[[TKT-5521]]","[[TKT-5530]]","[[TKT-5544]]","[[TKT-5561]]"]
  sobre_sku: ["[[SKU-CAF-2200]]","[[SKU-CAF-2201]]"]
```

**VEREDICTO: ✅ resuelto.** M7 ahora es gen del genoma base, no del seed; `sintesis_umbral` lo
hace configurable por sector y la derivación a EVOLVE convierte la señal en accionable. El
defecto deja de ser invisible de forma sistémica (no por suerte del seed_gen).

---

## Overall: PARTIAL

- Fricción 1: ✅  · Fricción 2: 🟡  · Fricción 3: ✅

Dos de tres quedan plenamente resueltas por el genoma nuevo. La 2 está resuelta a nivel de
GENOMA (los genes M1/M6 existen y el contenido ya los honra) pero **incompleta a nivel de
CONFIG del sandbox**: falta portar `relation_types` y `source_trust` desde
`company.example.yaml` a `sim/ecommerce/company.yaml`.

## Regresiones nuevas introducidas por los genes nuevos: 1 (de config, no de genoma)

- `gen-frontmatter-obligatorio` v3 cambió `relations` de set cerrado a **unión validada por
  LINT**. Para un manifiesto que NO declara sus verbos de dominio (como el `company.yaml` del
  sandbox actual), LINT pasaría de ignorar las claves extra a **marcarlas como no declaradas**
  (falsos positivos en SKU, reseñas y síntesis). Es regresión de migración, no de diseño: se
  cierra declarando los `relation_types` del e-commerce en el manifiesto. Ningún gen nuevo
  rompe el comportamiento ya observado en las páginas aplicadas.
