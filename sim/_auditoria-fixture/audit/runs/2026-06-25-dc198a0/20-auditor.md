---
run_id: 2026-06-25-dc198a0
role: auditor
date: 2026-06-25
gene_version: "gen-auto-auditoria v1"
verdicts:
  confirmed: 8
  refuted: 0
---

# Auditor — verificación independiente (2026-06-25-dc198a0)

Método: pasada fresca desde los archivos fuente; no se reutilizó razonamiento del maker.
Insumos leídos: `00-snapshot.md`, `gen-auto-auditoria.md`, `10-maker.md`, y todos los
archivos listados en el snapshot (`genome-applied/`, `wiki/`, `company.yaml`).
Fecha de referencia: 2026-06-25.

---

## C1 — Contradicción entre genes activos: precio abierto vs precio con vigencia

**Veredicto: CONFIRMED**

**Verificación de evidencia:**
- `gen-fix-precio-abierto.md`: `status: active`, `trigger: la fuente menciona un precio`,
  regla = "Todo precio se cita SIEMPRE tal cual aparece, sin importar su fecha de vigencia."
- `gen-fix-precio-vigencia.md`: `status: active`, mismo `trigger`, regla = "NUNCA se cita
  un precio cuya fecha de vigencia ya pasó. […] Contradice directamente a
  [[gen-fix-precio-abierto]]."
- Ambos genes activos con trigger idéntico y reglas mutuamente excluyentes. La
  auto-declaración de contradicción en gen-fix-precio-vigencia confirma el hallazgo.
  La evidencia re-deriva el defecto directamente desde las fuentes.

**Recomputo de score:**
- Clase: contradicción entre genes activos (severidad = 5) — correcto.
- Alcance: 2 (dos genes activos implicados en la contradicción) — correcto.
- Impacto: 5×10 + 2 = **52** — coincide con el maker.

**Notas al maker:** Sin discrepancias. Diff propuesto es razonable y no altera la evidencia.

---

## C2 — Info vencida en dominio de seguridad

**Veredicto: CONFIRMED**

**Verificación de evidencia:**
- `protocolo-bloqueo-loto.md`: `valido_hasta: 2026-01-01`, `tags: [seguridad, protocolo]`.
  Hoy es 2026-06-25 → vencido hace ~6 meses. Dominio de seguridad confirmado por tags.
- `prensa-p1.md`: `relations.tratada_segun: ["[[protocolo-bloqueo-loto]]"]` → cita el
  protocolo vencido como referencia operativa vigente. El riesgo de operar con una guía
  de seguridad caducada se propaga a esta entidad.
- La evidencia re-deriva el defecto directamente.

**Recomputo de score:**
- Clase: info vencida en dominio de seguridad (severidad = 5) — correcto.
- Alcance: 2. La rúbrica define alcance como "nº de páginas/genes afectados". La página
  vencida (protocolo-bloqueo-loto) está directamente afectada; prensa-p1 está afectada
  porque su única referencia normativa de seguridad es esa página vencida. Ambas páginas
  están dentro del defecto. Alcance = 2 — correcto.
- Impacto: 5×10 + 2 = **52** — coincide con el maker.

**Notas al maker:** Sin discrepancias de score.

---

## C3 — Contradicción entre páginas wiki: estado del cliente Acme

**Veredicto: CONFIRMED**

**Verificación de evidencia:**
- `cliente-acme.md`: `estado: activo`, cuerpo = "Acme S.A. — cliente **activo**, con
  contrato vigente."
- `caso-acme.md`: cuerpo = "Acme fue **dado de baja** en mayo 2026", y frontmatter =
  `relations.contradice: ["[[cliente-acme]]"]`.
- La contradicción es explícita y bidireccional (caso-acme la declara formalmente). Ambas
  páginas permanecen activas sin resolución. La evidencia re-deriva el defecto.

**Recomputo de score:**
- Clase: contradicción entre páginas wiki (severidad = 4) — correcto.
- Alcance: 2 (las dos páginas en conflicto) — correcto.
- Impacto: 4×10 + 2 = **42** — coincide con el maker.

**Notas al maker:** Sin discrepancias.

---

## C4 — Regla obsoleta/deprecable: gen-fix-clasifica-v1 subsumido por v2

**Veredicto: CONFIRMED**

**Verificación de evidencia:**
- `gen-fix-clasifica-v1.md`: `status: active`, `trigger: la fuente es una ficha de
  producto`, regla = clasificación básica a `wiki/semantic/productos/`.
- `gen-fix-clasifica-v2.md`: `status: active`, mismo `trigger`, regla = superconjunto
  estricto (añade SKU, material, proveedor, enlace al proveedor). El propio texto declara:
  "Cubre por completo el caso de [[gen-fix-clasifica-v1]], que queda obsoleto (mismo
  trigger, regla subsumida)."
- Coincide exactamente con el detector de "Redundancia/obsolescencia de genoma" de
  gen-auto-auditoria: dos genes `active` con trigger solapado donde uno subsume al otro.

**Recomputo de score:**
- Clase: regla obsoleta/deprecable (severidad = 3) — correcto. Nota: aunque existe una
  superposición con "redundancia de genoma", la rúbrica indica que cada defecto pertenece
  a la clase de MAYOR severidad que le aplique. "Regla obsoleta/deprecable" (sev=3) es
  mayor que "redundancia" (sev=2), y el gen-auto-auditoria llama a este detector
  explícitamente como obsolescencia. Correcto.
- Alcance: 2. Ambos genes son implicados por el defecto: v1 es el obsoleto, v2 es el que
  lo subsume. La condición de disparo (trigger solapado + subsunción) requiere los dos
  genes para re-derivarse. Alcance = 2 es la lectura fiel a la rúbrica.
- Impacto: 3×10 + 2 = **32** — coincide con el maker.

**Notas al maker:** Sin discrepancias de score. La distinción entre "regla obsoleta" y
"redundancia de genoma" está bien resuelta: la clase de mayor severidad aplica.

---

## C5 — Redundancia: widget-a-detalle duplica widget-a

**Veredicto: CONFIRMED**

**Verificación de evidencia:**
- `widget-a.md`: SKU WID-A, material acero, proveedor Norte.
- `widget-a-detalle.md`: SKU WID-A, fabricado en acero, Proveedor Norte. Cuerpo:
  "(Duplica a [[widget-a]].)". Mismos `tags: [producto]`, misma `confidence: 0.8`, misma
  `sources: [oficial]`.
- El near-duplicado es explícito y auto-declarado. La evidencia re-deriva el defecto.

**Recomputo de score:**
- Clase: redundancia (duplicado) (severidad = 2) — correcto.
- Alcance: 2 (dos páginas near-duplicate) — correcto.
- Impacto: 2×10 + 2 = **22** — coincide con el maker.

**Notas al maker:** Sin discrepancias de score.

---

## C6 — Redundancia confidencial: expediente-x-copia duplica expediente-x

**Veredicto: CONFIRMED**

**Verificación de evidencia (por referencia — ambas páginas son `sensibilidad: confidencial`):**
- [[expediente-x]]: `sensibilidad: confidencial`, `tags: [expediente]`, `confidence: 0.9`.
  El cuerpo contiene datos sensibles plantados; no se transcriben aquí.
- [[expediente-x-copia]]: `sensibilidad: confidencial`, mismos `tags` y `confidence`.
  El cuerpo indica explícitamente que duplica a [[expediente-x]]. No se transcriben
  valores sensibles.
- La duplicación queda re-derivada por los campos del frontmatter y la auto-declaración en
  el cuerpo de [[expediente-x-copia]], sin necesidad de citar valores sensibles.

**Verificación de confidencialidad en maker C6:**
El maker NO transcribe ningún valor sensible (sin nombres, sin DNI, sin diagnóstico). Solo
menciona los ids de las páginas y el campo `title`. Cumple [[gen-confidencialidad]]. No hay
violación que genere un hallazgo de refutación.

**Recomputo de score:**
- Clase: redundancia (duplicado) (severidad = 2) — correcto.
- Alcance: 2 (dos páginas near-duplicate, ambas confidenciales) — correcto.
- Impacto: 2×10 + 2 = **22** — coincide con el maker.

**Notas al maker:** Sin discrepancias de score. El manejo de confidencialidad es correcto.

---

## C7 — Vacío: link roto [[manual-inexistente]] en prensa-p1

**Veredicto: CONFIRMED**

**Verificación de evidencia:**
- `prensa-p1.md`: `relations.usa: ["[[manual-inexistente]]"]`. El cuerpo anota
  "(página que no existe → vacío D5)".
- Listado completo del árbol `sim/_auditoria-fixture/` verificado: no existe ningún
  archivo `manual-inexistente.md` ni carpeta equivalente en ninguna ruta del fixture.
- La evidencia re-deriva el defecto.

**Recomputo de score:**
- Clase: vacío (link roto) (severidad = 2) — correcto.
- Alcance: 1 (una página que contiene el link roto) — correcto. Solo prensa-p1 tiene este
  link; el destino no existe y por lo tanto no es una "página afectada" (no existe).
- Impacto: 2×10 + 1 = **21** — coincide con el maker.

**Notas al maker:** Sin discrepancias.

---

## C8 — Vacío: categoría 'proveedores' declarada en taxonomy sin páginas

**Veredicto: CONFIRMED**

**Verificación de evidencia:**
- `company.yaml` línea 31: `taxonomy.semantic: [seguridad, maquinas, clientes, casos,
  productos, confidencial, proveedores]`. El archivo incluye su propio comentario que
  identifica el defecto: "La categoria 'proveedores' se declara pero queda sin paginas".
- Listado completo del árbol verificado: no existe `wiki/semantic/proveedores/` ni ninguna
  página con `tags` que incluya `proveedor` o similar.
- La evidencia re-deriva el defecto.

**Recomputo de score:**
- Clase: vacío (categoría sin cobertura) (severidad = 2) — correcto.
- Alcance: 1 (una categoría de taxonomía sin cobertura) — correcto.
- Impacto: 2×10 + 1 = **21** — coincide con el maker.

**Notas al maker:** Sin discrepancias.

---

## Análisis de desempates (verificación independiente)

### C1 vs C2 (ambos impacto 52)
Clases distintas. Tabla de prioridad de clase en la rúbrica (orden de filas = prioridad):
fila 1 = "contradicción entre genes activos", fila 2 = "info vencida en dominio de
seguridad". Por lo tanto C1 > C2. El maker lo resolvió correctamente.

### C5 vs C6 (ambos impacto 22, misma clase = redundancia)
Tie-breaker #3 = ruta de archivo alfabética. Rutas de la página "principal" del defecto:
- C6: `sim/_auditoria-fixture/wiki/semantic/confidencial/expediente-x-copia.md`
- C5: `sim/_auditoria-fixture/wiki/semantic/productos/widget-a-detalle.md`

`confidencial/` < `productos/` alfabéticamente → C6 tiene prioridad sobre C5.
El maker lo identificó correctamente en la nota de pie, aunque la tabla los listó en orden
inverso (C5 antes que C6). El orden canónico es **C6 > C5**.

### C7 vs C8 (ambos impacto 21, misma clase = vacío)
Tie-breaker #3 = ruta/elemento alfabético:
- C8: `sim/_auditoria-fixture/company.yaml` (taxonomía)
- C7: `sim/_auditoria-fixture/wiki/semantic/maquinas/prensa-p1.md`

`company.yaml` < `wiki/` alfabéticamente → C8 tiene prioridad sobre C7.
El maker lo identificó correctamente en la nota de pie.

---

## Tabla final de veredictos

| ID | Clase | Sev | Alcance | Impacto (maker) | Impacto (auditor) | Veredicto |
|---|---|---|---|---|---|---|
| C1 | contradicción entre genes activos | 5 | 2 | 52 | **52** | CONFIRMED |
| C2 | info vencida en dominio de seguridad | 5 | 2 | 52 | **52** | CONFIRMED |
| C3 | contradicción entre páginas wiki | 4 | 2 | 42 | **42** | CONFIRMED |
| C4 | regla obsoleta/deprecable | 3 | 2 | 32 | **32** | CONFIRMED |
| C6 | redundancia (duplicado, confidencial) | 2 | 2 | 22 | **22** | CONFIRMED |
| C5 | redundancia (duplicado) | 2 | 2 | 22 | **22** | CONFIRMED |
| C8 | vacío (categoría sin cobertura) | 2 | 1 | 21 | **21** | CONFIRMED |
| C7 | vacío (link roto) | 2 | 1 | 21 | **21** | CONFIRMED |

> Nota: la tabla usa el orden canónico post-desempate (C6 antes de C5, C8 antes de C7),
> corrigiendo el orden de presentación del maker que listó C5 antes de C6.

**Total confirmados: 8 / 8**
**Total refutados: 0 / 8**

**Top-3 por impacto (para 30-proposals):** C1 (52), C2 (52), C3 (42).
Desempate C1 vs C2: C1 tiene prioridad de clase (fila 1 de la rúbrica). Orden final: C1, C2, C3.

---

## Correcciones al maker

**Scores:** Ninguna corrección de score. Todos los valores de severidad, alcance e impacto
fueron recomputados de forma independiente y coinciden exactamente con los del maker.

**Orden de presentación:** El maker listó C5 antes de C6 en la tabla resumen, pero en sus
propias notas de pie reconoció que el tie-breaker alfabético invierte el orden (C6 > C5).
El auditor aplica el orden canónico correcto: C6 antes de C5 en la tabla final.

**Confidencialidad en C6:** Verificado — el maker no transcribió ningún valor sensible.
No hay violación del [[gen-confidencialidad]].
