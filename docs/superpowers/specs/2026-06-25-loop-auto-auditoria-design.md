# Loop de auto-auditoría — diseño

- **Fecha:** 2026-06-25
- **Estado:** diseño aprobado (pendiente plan de implementación)
- **Capacidad:** operación `AUDIT` + `gen-auto-auditoria` + estado en `audit/runs/`
- **Autor:** arquitecto CEREBRO

---

## 1. Problema y objetivo

CEREBRO acumula conocimiento (wiki) y reglas (genoma) que, con el tiempo, desarrollan
**contradicciones, vacíos, reglas obsoletas y redundancias**. Hoy esos defectos solo se
detectan de forma reactiva (LINT mecánico) o por observación manual (el patrón
`findings.md` / `regresion.md` del banco `sim/`).

**Objetivo:** una capacidad **reproducible** que, al dispararse, audite la propia base
y proponga **las ≤3 mejoras de mayor impacto**, dejando todo trazado y sujeto a aprobación
humana. El valor entregado es el **mecanismo reproducible**, no un resultado puntual: debe
funcionar sobre cualquier base CEREBRO, sea cual sea el blueprint/sector cargado.

### No-objetivos (YAGNI)
- No aplica cambios por sí misma (solo propone).
- No reimplementa la detección que ya hacen LINT/CONSOLIDATE.
- No audita ni modifica `raw/`.
- No introduce ejecución de código obligatoria ni dependencias externas.

---

## 2. Encaje en la arquitectura actual

La capacidad es un **orquestador de alto nivel**, no un motor nuevo. Compone piezas
existentes y añade lo que falta:

| Pieza existente | Rol en AUDIT |
|---|---|
| [[gen-lint]] | Capa de detección mecánica (huérfanos, contradicciones, vencidos, esquema). |
| [[gen-consolidate]] | Detección de duplicados/decay (insumo de "redundancia"). |
| [[gen-evolve]] + [[gen-compuerta-mutacion]] | Canal por el que una propuesta aprobada que toca el genoma se aplica y se audita. |
| `genome/events.jsonl` + `log.md` | Trazabilidad ya establecida; AUDIT engancha aquí, no crea un tercer registro. |
| Patrón `sim/` (`findings.md` + `regresion.md`) | Molde del fixture de prueba reproducible. |

**Lo genuinamente nuevo:** (1) detector de **redundancia/obsolescencia a nivel de genoma**
(dos genes que se solapan o uno superado), que hoy solo ve EVOLVE ad hoc; (2) **ranking por
impacto**; (3) **verificación adversarial maker≠auditor**; (4) **corrida persistida y
reproducible** con foco "top-3".

---

## 3. Contrato del loop

- **Disparador:** invocación manual del verbo `AUDIT` ("auto-audítate / audita el cerebro").
  Más adelante, opcionalmente, el hook `Stop`. Explícito, nunca automágico.
- **Meta verificable (condición objetiva de "terminado"):** existe `audit/runs/<run-id>/`
  con `00-snapshot`, `10-maker`, `20-auditor` y `30-proposals`, donde `30-proposals` contiene
  **N = min(3, candidatos-confirmados-por-auditor)** propuestas, cada una con todos sus campos
  llenos (`id, fecha, motivo, evidencia, diff, score, status: pending`), **y** hay una línea
  en `log.md`. Chequeable por existencia de archivos y campos no vacíos → el loop no termina
  sin esto.
- **Maker ≠ Auditor (separación dura, ver §5).**
- **Estado persistente:** todo en disco (§6). Cada propuesta = registro con fecha, motivo
  (señal), evidencia citada, diff y `status: pending|approved|reverted`.
- **Gate de aprobación humana:** las ≤3 propuestas quedan `pending`. El humano aprueba/rechaza
  una por una. Solo entonces se aplican (§7).

---

## 4. Detección reproducible (resuelve P3-reproducibilidad)

El no-determinismo del LLM se acota dejando que la **identidad** de cada candidato salga de
**detectores mecánicos enumerables**, no de la "creatividad" del agente:

1. **Lectura en dos tiempos (respeta P3 — presupuesto de contexto):**
   - **Esqueleto estructural:** se lee solo el *frontmatter + grafo de relaciones* de las
     páginas (`relations, valido_hasta, confidence, status, version, sources, sensibilidad…`).
     Barato, estructurado, estable entre corridas. Es sobre lo que ya trabaja LINT.
   - **Drill-down:** se abre el *contenido completo* solo de las pocas páginas que un detector
     marcó como candidatas.
2. **Batería de detectores (reusa LINT + CONSOLIDATE):**
   - Huérfanos, contradicciones declaradas (`contradice`), vencidos (`valido_hasta < hoy`),
     verbos/campos fuera de esquema → LINT.
   - Duplicados / near-duplicados → CONSOLIDATE.
   - **Redundancia/obsolescencia de genoma (nuevo):** dos genes con `trigger` solapado o uno
     cuya regla quedó subsumida/contradicha por otro.
3. **El LLM solo juzga y redacta:** decide "¿este candidato importa?" y redacta el `diff`
   propuesto. No decide *qué* página ni *qué* clase de defecto (eso lo fija el detector).

**Frontera de reproducibilidad declarada:** *mismo estado (mismo SHA de git) + misma versión
de `gen-auto-auditoria` ⇒ mismo conjunto de candidatos y mismo ranking; solo varía la
redacción de la prosa del diff.* Esto cumple el espíritu de "mismas propuestas" sin prometer
identidad byte-a-byte, que sería falsa en cualquier sistema con LLM.

---

## 5. Roles maker ≠ auditor (barrera en disco)

La separación se garantiza por **barrera de información en disco**, no por procesos separados
(para correr en cualquier agente — portabilidad markdown):

- **Maker:** insumos = `00-snapshot` + `gen-auto-auditoria` (criterios). Corre la batería de
  detectores sobre el esqueleto, hace drill-down de candidatos, y produce **todos** los
  candidatos con evidencia + `diff` propuesto + score auto-calculado. Escribe `10-maker.md`.
- **Auditor:** pasada **fresca** cuyos **únicos** insumos son `00-snapshot` + el gen +
  `10-maker.md` (NO la memoria de la sesión ni el razonamiento privado del maker). Su tarea:
  (a) **refutar** cada candidato; (b) verificar que la evidencia citada **re-deriva** el
  hallazgo; (c) **recalcular** el score con la fórmula. Escribe `20-auditor.md`.
- **Orquestador (el agente principal):** ensambla `30-proposals.md` con las ≤3 confirmadas y
  rankeadas. Nunca juzga sus propios hallazgos.

**Subagentes** (Agent tool / Workflow) son una **optimización opcional** donde el runtime los
soporte; el canon portable es el hand-off en disco.

### Rúbrica de impacto (embebida en `gen-auto-auditoria`, gateada por `version`)

- `severidad` por clase de defecto (tabla fija):
  - contradicción entre genes activos = 5
  - info citada vencida en dominio de seguridad = 5
  - contradicción entre páginas wiki = 4
  - regla obsoleta/deprecable = 3
  - vacío (link roto / categoría sin cobertura) = 2
  - redundancia (duplicado) = 2
- `alcance` = nº de páginas/genes afectados (contable).
- `impacto = severidad * 10 + alcance`.
- **Desempate determinista:** prioridad de clase (orden de la tabla) → ruta de archivo
  alfabética.
- **Top-N:** las N de mayor `impacto`, con N = min(3, confirmadas por auditor).

Cambiar esta rúbrica = editar el gen = subir `version` = pasa por [[gen-compuerta-mutacion]]
+ línea en `events.jsonl`. No hay archivo de criterios suelto que pueda derivar sin auditoría.

---

## 6. Estado persistente y reproducibilidad

- **Identidad del estado:** `git rev-parse HEAD` con árbol limpio (`git status`). Si el árbol
  está sucio, se registra `HEAD + dirty` y la lista de archivos modificados.
- **`run-id` = `<YYYY-MM-DD>-<short-SHA>`.** Idempotente por SHA: si ya existe `runs/` para
  ese SHA, AUDIT lo reconoce y no duplica (P2).
- **Carpeta `audit/runs/<run-id>/`** (estado operacional, registrada en `index.md` y en el
  mapa de `CLAUDE.md`):
  - `00-snapshot.md` — SHA, árbol limpio/sucio, lista de archivos auditados, versión del gen,
    disparador, fecha.
  - `10-maker.md` — todos los candidatos con evidencia + diff + score.
  - `20-auditor.md` — veredicto por candidato (confirmado/refutado) + re-derivación + score
    recalculado.
  - `30-proposals.md` — las ≤3 finales rankeadas, cada una `status: pending` con
    `id/fecha/motivo/diff/evidencia/score`. Las decisiones humanas se anotan aquí mismo
    (`status: approved|reverted`).
- **Reconstrucción:** todo insumo (snapshot), criterio (versión del gen) y salida está en
  disco; cualquiera puede re-verificar que el top-3 siguió la fórmula sin re-correr el LLM.

---

## 7. Gate humano y aplicación

1. Las ≤3 propuestas quedan `status: pending`.
2. El humano aprueba/rechaza una por una.
3. Aplicación de las aprobadas:
   - **Tocan genoma** → por [[gen-compuerta-mutacion]]: aplicar + subir `version` + 1 línea en
     `events.jsonl` (`signal` cita `audit/runs/<id>#Pn`) + commit + re-sync `AGENTS.md`.
   - **Tocan contenido wiki** → cambio directo + línea en `log.md` + commit (igual que
     CONSOLIDATE; sin `events.jsonl`).
4. **Revertir** = `git revert` del commit + actualizar `status: reverted` en `30-proposals.md`.

---

## 8. Confidencialidad (hereda [[gen-confidencialidad]])

Los artefactos de auditoría se persisten y commitean; por tanto **no pueden citar contenido
sensible**. Para páginas `sensibilidad: confidencial`:
- Se auditan, pero la evidencia se expresa por **referencia** (`[[link]]` / id + nombre de
  campo inconsistente), **nunca** volcando el valor sensible.
- Si se usan subagentes, no se les envía el texto sensible verbatim.

---

## 9. Cumplimiento de los principios inviolables

1. **`raw/` inmutable** → AUDIT solo lee; ninguna propuesta edita `raw/`. ✅
2. **Idempotencia** → corrida claveada al SHA; rerun no duplica. ✅
3. **Presupuesto de contexto** → lectura esqueleto→drill-down; no carga la wiki entera. ✅
4. **Mutación con compuerta** → rúbrica en el gen y propuestas de genoma pasan por la
   compuerta. ✅
5. **Auditabilidad** → `events.jsonl` para genoma; `audit/runs/` + `log.md` para la corrida. ✅
6. **`[[wiki-link]]` + frontmatter** → el gen nace con frontmatter; artefactos de corrida son
   estado operacional (exentos como `log.md`) pero referencian por `[[link]]`. ✅
7. **Preguntar antes de borrar/sobrescribir lo ajeno** → AUDIT propone, nunca aplica solo. ✅

---

## 10. Prueba reproducible sin caso real (fixture)

Banco autocontenido en `sim/_auditoria-fixture/` (prefijo `_` = prueba de capacidad, no
vertical de industria), reusando el molde `sim/`:

- Base CEREBRO mínima con **defectos plantados y conocidos**:
  - 2 genes que se contradicen (contradicción / regla obsoleta).
  - 2 páginas wiki que se contradicen.
  - 1 página con `[[link roto]]` + 1 categoría sin cobertura (vacío).
  - 2 páginas casi-duplicadas (redundancia).
  - 1 página `valido_hasta` vencida.
  - 1 página `sensibilidad: confidencial` con un defecto (para probar §8).
- `expected.md` — el **top-3 esperado** dado la fórmula (oráculo de reproducibilidad).
- `regresion.md` — registra una corrida real del loop sobre el fixture y la compara con
  `expected.md`.

**Criterio de prueba pasada:** la auditoría aflora los defectos plantados de alto impacto, la
fórmula los rankea igual que `expected.md` (top-3), y la evidencia de la página confidencial
NO cita su contenido. Re-correr reproduce el conjunto y el ranking (la prosa del diff puede
variar). No usa datos de empresa; es re-ejecutable y aislado (no toca el cerebro real).

---

## 11. Riesgos y decisiones cerradas

**Riesgos:**
1. No-determinismo en la redacción de diffs → acotado a prosa; identidad y ranking
   deterministas (§4).
2. Independencia maker/auditor → garantizada por barrera en disco; subagentes opcionales (§5).
3. Solape con LINT/CONSOLIDATE → AUDIT los **consume**; no reimplementa detección (§2, §4).
4. Alcance ilimitado → la rúbrica fija el alcance; presupuesto = top-3.
5. Deriva del fixture → `expected.md` se versiona junto al gen; si cambia la rúbrica, se
   re-deriva.

**Decisiones cerradas:** alcance = genoma + wiki · verbo = `AUDIT` · disparo = manual (hook
`Stop` en fase posterior) · gen único (sin cápsula por ahora) · criterios embebidos en el gen
· identidad por git SHA · artefactos en `audit/runs/`.

---

## 12. Entregables de implementación (resumen para writing-plans)

1. `genome/genes/gen-auto-auditoria.md` (gen nuevo, con rúbrica embebida) — vía compuerta.
2. Registro del gen en `CLAUDE.md` (tabla de operaciones + índice de genes) y re-sync de
   `AGENTS.md`.
3. Registro de `audit/` en `index.md` y en el mapa de `CLAUDE.md`.
4. `audit/` con `README` y `runs/.gitkeep`.
5. Fixture `sim/_auditoria-fixture/` con defectos plantados + `expected.md` + `regresion.md`.
6. Línea(s) en `genome/events.jsonl` por el alta del gen (compuerta) + commit.
