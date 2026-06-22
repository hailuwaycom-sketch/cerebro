# regresion.md — REGRESIÓN (sandbox PRODUCCIÓN / Forjados del Valle S.A.)

Verifica si las fricciones reportadas contra el genoma BASE quedan resueltas por el genoma
NUEVO (16 genes). Solo lectura del genoma; reutiliza el escenario ya derivado en el sandbox.
Fecha de regresión: 2026-06-22.

Genoma evaluado: [[gen-frontmatter-obligatorio]] v3, [[gen-clase-temporal]],
[[gen-entidad-con-estado]], [[gen-vigencia-temporal]], [[gen-sintesis-de-volumen]],
[[gen-confianza-por-fuente]], [[gen-confidencialidad]].

---

## F1 → M1 — relations dejaba de ser set cerrado (verbos de trazabilidad)

**Antes:** `relations{usa,depende_de,contradice,reemplaza}` cerrado; hubo que inventar
`producido_en`, `afecta_a`, `mantiene_a` que ningún LINT validaría.

**Re-derivación con el gen nuevo:** [[gen-frontmatter-obligatorio]] v3 afirma que `relations`
**ya no es un set cerrado**: núcleo reservado ∪ `relation_types` declarados en
`onboard/company.yaml`; LINT valida contra esa unión. El mecanismo SEMÁNTICO existe y encaja
exactamente con la trazabilidad direccional (lote `producido_en` máquina, NCR `afecta_a` lote,
mtto `mantiene_a` máquina). Las páginas ya usan verbos de dominio sin colisionar con el núcleo.

**Hallazgo de regresión (config, no de diseño):** el manifiesto del sandbox
`sim/produccion/company.yaml` **NO declara el bloque `relation_types`** (sí está en
`onboard/company.example.yaml`). Verbos hoy en uso y no declarados: `producido_en, bajo_orden,
de_producto, insumo_de, afectado_por, afecta_a, viola, causa_en, mitiga_con, ubicada_en,
produjo_lote, intervenida_por, mantiene_a, resuelve`. Con el gen v3, LINT los marcaría TODOS
como verbos huérfanos hasta poblar `relation_types`. La capacidad está; falta el dato de onboard.

**Veredicto: 🟡 parcial** — el gen resuelve el problema de diseño (relaciones extensibles +
validables), pero el escenario quedaría rojo en LINT hasta declarar `relation_types` en el
manifiesto de producción. Acción: añadir al `company.yaml` los 14 verbos de trazabilidad.

---

## F2 → M4 (gen-clase-temporal) — estable vs evento fechado

**Antes:** sin regla; NCR de 8 meses decae igual que ficha técnica vigente; resuelto a mano.

**Re-derivación:** [[gen-clase-temporal]] obliga `clase: estable|evento`. Aplicado al escenario:
- `LOTE-SM45-2606`, `PR-200-prensa`, `soporte-motor-SM45` → **estable** (decay_rate low).
- `NCR-2026-031` → **evento**, `fecha_evento: 2026-06-19`, decay_rate high, NO se refuerza por
  reingesta. (La página ya tiene `decay_rate: high` y `fecha_evento`; falta solo el campo
  literal `clase: evento`, hoy implícito en el cuerpo "evento fechado".)
- `MTTO-PR-200-2026-06` → **evento**, `fecha_evento: 2026-06-19`, decay high. Idem.

CONSOLIDATE/LINT ahora decaen y promueven por `clase`, no por convención de tags. Los volatile
fields (p.ej. precio/stock) tendrían semántica a nivel de campo. El criterio que faltaba existe.

**Veredicto: ✅ resuelto** — la distinción estable/evento es ahora regla del genoma y el
escenario la cumple en sustancia (decay diferenciado + fecha_evento + no-refuerzo). Mejora
menor: añadir el campo explícito `clase:` al frontmatter de NCR y MTTO para que LINT lo lea sin
inferir.

---

## F3 → M5 (gen-entidad-con-estado) — estado mutable vs idempotencia

**Antes:** [[gen-ingest]] "si existe, actualiza y refuerza" no distinguía actualizar un campo
de estado in-place de registrar un evento histórico; riesgo de sobrescribir historial o duplicar.

**Re-derivación sobre la máquina PR-200:** [[gen-entidad-con-estado]] dice: entidad con campo
`estado` se actualiza **in-place** (no se duplica) y **todo cambio de estado debe originarse en
una página `clase: evento` que la enlace**. El escenario lo cumple exactamente:
- `PR-200-prensa` mantiene `estado: operativa` + `ultimo_mantenimiento` en UNA sola página.
- El cambio de estado se respalda con el evento `MTTO-PR-200-2026-06` (`mantiene_a` →
  `PR-200-prensa`), que es `clase: evento`. Historial preservado en el rastro de eventos.
- LINT marcaría inconsistencia si el estado cambiara sin evento de respaldo: aquí SÍ hay respaldo.

**Veredicto: ✅ resuelto** — idempotencia preservada (in-place) + historial garantizado por
el evento de respaldo. El choque queda eliminado por diseño y verificado en el caso real.

---

## Comprobaciones de paso

### M2 — vigencia dura ([[gen-vigencia-temporal]])
**Aplicabilidad a producción: baja en este lote.** Ninguna de las 3 fuentes lleva caducidad
dura natural (NCR cerrada, OP cerrada, MTTO fechado). Donde SÍ aplicaría: certificados de
colada (2026-CN-5521), calibraciones de galga, certificaciones de proveedor y `proximo_preventivo`
de la máquina (un MP vencido debería advertirse). Hoy `proximo_preventivo: 2026-07-19` está en
PR-200 como campo informativo, no como `valido_hasta`. **Veredicto: ✅ disponible / no crítico
aquí** — el gen está listo; oportunidad: modelar `proximo_preventivo` como vigencia dura para que
QUERY advierta MP vencido.

### M7 — síntesis de volumen ([[gen-sintesis-de-volumen]])
**Aplicabilidad: latente.** Con `sintesis_umbral` (default 3, declarado en example.yaml, ausente
en el company.yaml de producción), ≥N eventos con clave común (misma máquina+falla, mismo
SKU+defecto) generan una `type: sintesis` que destila el patrón y puede derivar a EVOLVE.
En este sandbox solo hay **1** NCR de PR-200 → no se dispara aún. El gen es correcto y útil
para el vertical (defecto sistémico de troquel se volvería visible al acumular). **Veredicto:
✅ disponible / no se dispara con N=1** — falta también declarar `sintesis_umbral` en el
company.yaml de producción.

---

## Veredicto global

| Fricción | Mutación | Veredicto |
|---|---|---|
| F1 relations set cerrado | M1 (frontmatter v3 + relation_types) | 🟡 parcial (falta declarar relation_types en company.yaml de prod) |
| F2 estable vs evento | M4 (gen-clase-temporal) | ✅ resuelto |
| F3 estado mutable vs idempotencia | M5 (gen-entidad-con-estado) | ✅ resuelto |
| M2 vigencia dura (de paso) | gen-vigencia-temporal | ✅ disponible / no crítico aquí |
| M7 síntesis de volumen (de paso) | gen-sintesis-de-volumen | ✅ disponible / no se dispara (N=1) |

**Overall: PARTIAL** — el diseño del genoma nuevo resuelve las 3 fricciones; F1 queda en
parcial por configuración faltante en el manifiesto de producción (no por el gen).

**Regresiones nuevas introducidas por los genes nuevos: ninguna en el comportamiento.**
Única deuda detectada: el `company.yaml` de producción quedó desincronizado del esquema de
onboard nuevo — no declara `relation_types` ni `sintesis_umbral` (sí están en
`company.example.yaml`), y los frontmatter de NCR/MTTO aún no escriben el campo literal `clase:`.
Son ajustes de datos/onboard, no defectos de los genes.
