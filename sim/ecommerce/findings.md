# EVOLVE — fricciones del genoma base y propuestas de mutación

Sandbox: `sim/ecommerce/` (TiendaNube Andina). Las propuestas NO se aplican a
`D:/cerebro/genome/`: pasan por [[gen-compuerta-mutacion]] (aprobación + `events.jsonl` + commit).

---

## (a) Fricciones — dónde el genoma BASE se quedó corto

1. **`decay_rate` es de página, no de campo.** [[gen-frontmatter-obligatorio]] define `decay_rate`
   como un atributo único de la página entera. Pero una ficha de producto mezcla specs estables
   (`low`) con precio/stock volátiles (`high`) en la MISMA entidad estable por SKU. El genoma base
   no tiene cómo expresar "esta página es estable salvo estos 2 campos que vencen". Tuve que
   inventar un campo `valido_a` por dato dentro del cuerpo para no fragmentar el SKU en dos
   páginas (lo que violaría [[gen-sku-identidad]]). Fricción real entre identidad estable y
   volatilidad de datos.

2. **No hay vocabulario de relaciones del dominio ni confianza por tipo de fuente.**
   [[gen-frontmatter-obligatorio]] fija `relations` a `{usa, depende_de, contradice, reemplaza}`.
   El e-commerce necesita `sobre_sku`, `variante_de`, `suministra`, `abrio_ticket`, `agrega`:
   las metí como claves extra, pero quedan fuera del contrato del gen, así que [[gen-lint]] y
   [[gen-query]] no las "entienden" oficialmente. Además, una reseña es señal blanda no
   verificada: la modelé con `confidence: 0.4` a mano, pero nada en el genoma obliga a degradar
   la confianza por *clase de fuente* (review vs. ficha oficial) — depende del criterio del agente.

3. **El umbral de síntesis y el disparo a EVOLVE son del seed_gen, no del genoma base.**
   [[gen-consolidate]] habla de fusionar duplicados y mover tiers, pero NO de **agregar N
   eventos episódicos de alto volumen en una página de patrón** (3+ tickets → síntesis). Sin
   `gen-sintesis-tickets` (sembrado), el genoma base dejaría 4 tickets sueltos sin detectar el
   defecto sistémico. El retail (miles de tickets/reseñas casi idénticos) hace de esto una
   necesidad estructural, no un caso de borde.

---

## (b) Propuestas de mutación al genoma BASE (concretas)

1. **EDITAR [[gen-frontmatter-obligatorio]]** (v1 → v2): de `decay_rate` como atributo único de
   página → a permitir **`decay_rate` por campo**: un `decay_rate` de página (default) MÁS un
   bloque opcional `volatile_fields: [{campo, decay_rate, valido_a}]`. Así una entidad estable
   declara campos que vencen sin partirse en dos páginas. Resuelve la fricción (1).

2. **NUEVO `gen-confianza-por-fuente`**: "Al ingerir, la `confidence` inicial se ancla al tipo
   de documento del manifiesto: fuentes oficiales (ficha, política, contrato) nacen ≥0.85;
   señales blandas no verificadas (reseña, comentario, redes) nacen ≤0.5 y entran como
   `relations.refuerza`/`corrobora`, nunca como afirmación única; marcar el tipo de señal en
   `tags`." `target_tier: semantic`. Resuelve parte de la fricción (2) y vuelve determinista lo
   que hoy es criterio del agente.

3. **NUEVO `gen-sintesis-de-volumen`** (generaliza el seed `gen-sintesis-tickets` al genoma base):
   "Cuando ≥N entidades episódicas (`decay_rate: high`) comparten una misma clave (entidad
   referida o causa raíz), CONSOLIDATE crea/actualiza una página `type: sintesis` (`decay_rate:
   low`) que las `agrega` y enlaza a la entidad común; si el patrón implica defecto/riesgo,
   deriva a [[gen-evolve]]. N configurable por sector (default 3)." `target_tier: semantic`.
   Resuelve la fricción (3) y extiende [[gen-consolidate]] / [[gen-evolve]] a cualquier vertical
   de alto volumen, no solo retail.
