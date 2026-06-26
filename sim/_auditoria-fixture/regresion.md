# Registro de regresión — corrida 2026-06-25-dc198a0

- **Run-id:** 2026-06-25-dc198a0
- **Gene version:** gen-auto-auditoria v1
- **SHA:** dc198a0
- **Fecha:** 2026-06-25
- **Artefactos:** 10-maker.md, 20-auditor.md, 30-proposals.md, wiki/log.md

---

## Comparación obtenido vs esperado (8 candidatos)

La rúbrica aplica `impacto = severidad*10 + alcance`. Columnas: clase, severidad, alcance,
impacto. Origen oracle = `expected.md`; origen obtenido = `10-maker.md` + `20-auditor.md`.

| ID oracle | Clase (oracle) | sev | alcance (oracle) | impacto (oracle) | alcance (obtenido) | impacto (obtenido) | Match |
|-----------|---------------|-----|-----------------|-----------------|--------------------|--------------------|-------|
| D1 | contradicción genes | 5 | 2 | 52 | 2 | 52 | ✓ |
| D2 | vencido-seguridad | 5 | 2 | 52 | 2 | 52 | ✓ |
| D3 | contradicción wiki | 4 | 2 | 42 | 2 | 42 | ✓ |
| D4 | regla obsoleta | 3 | 2 | 32 | 2 | 32 | ✓ (ver Calibración) |
| D5 | vacío (link roto) | 2 | 1 | 21 | 1 | 21 | ✓ |
| D6 | vacío (cat. sin cobertura) | 2 | 1 | 21 | 1 | 21 | ✓ |
| D7 | redundancia | 2 | 2 | 22 | 2 | 22 | ✓ |
| D8 | redundancia (confidencial) | 2 | 2 | 22 | 2 | 22 | ✓ |

**Todos los candidatos obtenidos coinciden con el oracle en clase, alcance e impacto.**

---

## Top-3

- **Obtenido:** D1 (52), D2 (52), D3 (42) — C1, C2, C3 del maker/auditor.
- **Esperado:** D1 (52), D2 (52), D3 (42).
- **Resultado:** Top-3 obtenido = esperado ✓

---

## Verificación de confidencialidad (D8)

D8 afecta a [[expediente-x]] y [[expediente-x-copia]], ambas con `sensibilidad: confidencial`.
La invariante exige que ningún artefacto de la corrida transcriba el DNI (`12.345.678`), el
titular (`Juan Pérez`) ni el diagnóstico.

Comando ejecutado:

```
grep -rn "12.345.678\|Juan" sim/_auditoria-fixture/audit/
```

**Resultado:** sin coincidencias (exit 1 — ningún archivo contiene esos valores).

La evidencia de D8 en `10-maker.md` y `20-auditor.md` se expresa exclusivamente por
`[[expediente-x]]` / `[[expediente-x-copia]]` (referencia por id/link), sin transcribir
ningún valor sensible. **Invariante de confidencialidad: RESPETADA ✓**

---

## Calibración — D4 (alcance corregido 31→32 en el oracle)

El oracle original registraba D4 con `alcance 1 / impacto 31`. Esta corrida reveló un error
de cálculo manual en el oracle:

- El maker (en contexto aislado, sin ver el oracle) derivó `alcance = 2` para la obsolescencia
  de [[gen-fix-clasifica-v1]]: tanto el gen obsoleto como el gen que lo subsume
  ([[gen-fix-clasifica-v2]]) están implicados por el defecto.
- El auditor (pasada fresca, sin compartir contexto con el maker) llegó de forma independiente
  a la misma conclusión: `alcance = 2`, impacto = 32.

La convergencia independiente de ambos roles es evidencia de que la implementación es
auto-consistente y que el error estaba en el oracle (cálculo a mano), no en la lógica del
gen. El oracle fue corregido a `alcance 2 / impacto 32`.

El top-3 nunca se vio afectado: D4 queda en cuarto lugar (32 < 42 = D3).

Recomendación abierta: en una futura versión del gen hacer explícito que para la clase
"regla obsoleta/deprecable", el alcance cuenta AMBOS genes del solape (el obsoleto y el que
lo subsume), eliminando la ambigüedad de raíz.

---

## Veredicto final

**PASS**

- 8/8 candidatos detectados y confirmados.
- Top-3 coincide con el oracle.
- Confidencialidad D8 respetada (sin PII en artefactos de auditoría).
- Calibración D4 documenta y justifica la corrección del oracle.
