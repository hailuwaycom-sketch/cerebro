# regresion.md — AGENCIA (FlujoLab) vs genoma NUEVO (16 genes)

Verificación de las 3 fricciones del `findings.md` contra los genes mutados.
Re-derivación aplicada a casos REALES del sandbox. NADA fuera del sandbox fue modificado.

---

## F1 — relations cerrado + `reemplaza` ambiguo  → M1 (gen-frontmatter v3 + relation_types) + M5 (gen-entidad-con-estado)

Caso: `wiki/semantic/leads/lead-inmobiliaria-vertice.md` (verbos usados: `recibio_propuesta`, `tiene_followup`).

**M1 (apertura del set):** gen-frontmatter v3 declara `relations` ya NO cerrado: núcleo `{usa, depende_de,
contradice, reemplaza}` ∪ `relation_types` de `onboard/company.yaml`. LINT valida contra esa UNIÓN.
Re-derivación: el verbo `recibio_propuesta` queda LEGAL **solo si** la empresa lo declara.
→ PROBLEMA DE CONFIGURACIÓN: el `company.yaml` del sandbox (FlujoLab) **NO tiene bloque `relation_types`**.
Solo el `company.example.yaml` (Acme Bots) lo trae (`[recibio_propuesta, proviene_de, define_precio]`).
Con el genoma nuevo + el manifiesto actual de FlujoLab, LINT marcaría `recibio_propuesta`, `tiene_followup`,
`define_precio`, `proviene_de`, `reemplaza_por` como **verbos no declarados**. El gen RESUELVE el mecanismo;
falta cerrar el loop en el manifiesto del sandbox (añadir `relation_types: [recibio_propuesta, tiene_followup,
define_precio, proviene_de, reemplaza_por]`). Además `tiene_followup` y `reemplaza_por` NI SIQUIERA están en
el example → el set declarable está incompleto para este vertical.

**M5 (ambigüedad reemplaza vs conversión):** gen-entidad-con-estado separa explícitamente `reemplaza`
(sustitución de fuente, la vieja se deprecia) de `sucede_a`/`proviene_de` (cambio de estado CON conservación).
Regla dura: "lead→cliente: la anterior NO se borra; se enlazan con relación tipada conservando trazabilidad".
Re-derivación de la conversión: el lead conserva `estado: convertido` + `reemplaza_por`→cliente; el cliente
lleva `proviene_de`→lead; el cambio de estado debe originarse en una página `clase: evento` (la firma) que lo
respalde, y LINT marca todo `estado` que cambió sin evento de respaldo. → El riesgo de "un agente borra el lead
usando `reemplaza`" queda CERRADO semánticamente.

**Veredicto F1: 🟡 PARCIAL.** M5 elimina la ambigüedad de borrado (✅). M1 da el mecanismo de extensión (✅)
pero el manifiesto del sandbox no declara los verbos, así que HOY LINT los rechazaría; y el `relation_types`
del example no cubre `tiene_followup`/`reemplaza_por`.

---

## F2 — QUERY citó precio vencido como vigente (decay ≠ fecha dura)  → M2 (gen-vigencia-temporal)

Caso: `wiki/semantic/precios/precio-vertice-PROP-2026-038.md` — `valido_hasta: 2026-06-14`; hoy 2026-06-22.

Re-derivación contra gen-vigencia-temporal v1: "QUERY, LINT y CONSOLIDATE comparan `valido_hasta` contra hoy;
si pasó, se marca vencida y QUERY la advierte SIEMPRE antes de citarla, con independencia de confidence/decay_rate".
Aplicado: 2026-06-14 < 2026-06-22 → VENCIDO. confidence 0.9 ya NO basta para citarlo limpio; QUERY debe
anteponer la advertencia. La `query-demo.md` ya refleja exactamente este comportamiento (⚠️ "Está VENCIDO...
no cotizarlo como vigente"). El gen GENERALIZA el seed ad-hoc `gen-precio-perecedero` a cualquier vertical
(contratos, certificaciones, stock) y desacopla vigencia dura de decay blando.
Nota: el campo es ahora OPCIONAL canónico en gen-frontmatter v3 (`valido_hasta`), no un invento de seed.

**Veredicto F2: ✅ RESUELTO.** La comparación de fecha dura es regla de genoma; ya no depende de sembrar un gen.

---

## F3 — sin ciclo de vida para accionables con fecha (follow-ups)  → M4 (gen-clase-temporal)

Caso: `wiki/working/followup-vertice-2026-05-16.md` — `fecha_objetivo: 2026-05-16`, `estado: vencido`.

Ojo: el findings proponía M3 `gen-accionables` (+ tier `tasks/` o `type: accionable`). El genoma nuevo NO
creó ese gen; en su lugar trae M4 **gen-clase-temporal** (`clase: estable|evento` + `fecha_evento`) y M5
gen-entidad-con-estado (`estado`). Re-derivación del follow-up con M4:
- Es un EVENTO accionable, no conocimiento estable → `clase: evento`, `decay_rate: high`, NO se refuerza por
  reingesta (cada follow-up es un registro distinto). ✓ encaja.
- M4 exige `fecha_evento` para los eventos; el follow-up usa `fecha_objetivo` (semántica de deadline, no de
  "cuándo ocurrió"). M4 no define el campo `fecha_objetivo` ni la transición de deadline.
- El ciclo de vida del pendiente (abierto→hecho/vencido, escalar/cerrar al pasar el deadline) lo gobierna M5
  vía `estado`, PERO M5 pide que el cambio de estado se origine en una página `clase: evento`; "deadline pasado
  sin cerrar" como hallazgo de LINT NO está escrito en ningún gen nuevo (gen-vigencia-temporal habla de
  `valido_hasta`, no de `fecha_objetivo` de accionables).

**Veredicto F3: 🟡 PARCIAL.** M4+M5 dan hogar canónico (`clase: evento` + `estado`) y evitan que el follow-up
se fusione/refuerce — mejor que meterlo a mano en working/. Pero NO hay regla explícita de "deadline vencido
sin cerrar → LINT lo escala", ni el campo `fecha_objetivo` es canónico (queda como opcional informal). El hueco
conocimiento↔tarea se mitiga, no se cierra del todo.

---

## Regresiones nuevas introducidas por los genes nuevos
- **gen-frontmatter v3 estricto + manifiesto desactualizado:** al endurecer LINT contra `relation_types`
  declarados, las páginas YA EXISTENTES del sandbox (lead, precio, follow-up) que usan `recibio_propuesta`,
  `tiene_followup`, `define_precio` PASARÍAN A SER INVÁLIDAS hasta que FlujoLab declare esos verbos. Es una
  regresión de validación inducida por la mutación (antes "fuera de esquema" tolerado; ahora "verbo ilegal").
- **clase default `estable`:** gen-clase-temporal pone `clase: estable` por defecto; las páginas-evento ya
  escritas (calls, follow-up) no llevan `clase` y serían marcadas/recategorizadas en relint. Migración pendiente.
- Sin contradicciones entre genes detectadas (M2 y M4 se reparten vigencia dura vs clase de evento de forma coherente).

## Overall: PARTIAL
F2 resuelto; F1 y F3 parciales por gaps de configuración (relation_types incompletos en el manifiesto) y por
faltar la regla de ciclo de vida de accionables con deadline (el M3 propuesto no se implementó como tal).
