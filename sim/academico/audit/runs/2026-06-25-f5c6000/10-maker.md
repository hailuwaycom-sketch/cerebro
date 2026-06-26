---
run_id: 2026-06-25-f5c6000
scenario: academico
role: maker
fecha: 2026-06-25
gen_auditoria_version: 1
insumos: [00-snapshot.md, gen-auto-auditoria.md]
candidatos_totales: 5
---

# 10-Maker — Candidatos · Run 2026-06-25-f5c6000

Producido por el rol **Maker** a partir únicamente de `00-snapshot.md` + `gen-auto-auditoria.md`.
Sin memoria de sesión previa. TUNING mode activo (rubrica aplicada honestamente; fricciones del gen al final).

---

## Candidatos

### C-01 — Contradicción de estimaciones entre versiones del mismo estudio

| Campo | Valor |
|---|---|
| **Clase** | contradicción entre páginas wiki |
| **Severidad** | 4 |
| **Afectados** | [[wp-2024-07]], [[giea-2025-03]] |
| **Alcance** | 2 páginas |
| **Impacto** | 4×10 + 2 = **42** |

**Evidencia.**
`[[wp-2024-07]]` (working-paper, `confidence: 0.5`, `tag: supersedido`) reporta en su cuerpo elasticidad precio de corto plazo = **−0.8** (VI, n ≈ 28.000, excluye estrato 1).
`[[giea-2025-03]]` (paper publicado del mismo estudio, `confidence: 0.9`) reporta elasticidad revisada = **−0.5** (VI con controles adicionales de ingreso y composición del hogar; muestra corregida, n ≈ 26.800).

La relación `supersede` está correctamente marcada (`giea-2025-03 → supersede → wp-2024-07`), y `wp-2024-07` lleva `tag: supersedido`. Sin embargo, ambas páginas coexisten activas en `semantic/` con el mismo campo empírico (elasticidad de la demanda residencial de energía) expresado en cifras distintas sin nota que deje claro en la página del WP que la estimación fue **revisada** — no simplemente actualizada sin cambio numérico.

El riesgo concreto: un QUERY sobre "elasticidad energía residencial Colombia" devuelve ambas páginas si no hay priorización explícita, y un lector no avisado puede citar −0.8 siendo que el valor publicado es −0.5. La discrepancia es sustancial (37% de diferencia relativa) y metodológicamente relevante (la corrección de muestra y los controles adicionales cambian el signo del debate sobre inelasticidad).

**Diff propuesto.**
En `[[wp-2024-07]]`: añadir bajo el bloque de estimación una nota explícita:
```
> ⚠ Estimación preliminar. La versión publicada [[giea-2025-03]] revisa este resultado
> a −0.5 tras controles de ingreso y corrección de muestra de estrato 1. Usar
> [[giea-2025-03]] para toda cita y consulta (ver [[gen-version-paper]]).
```
No se cambia el número del WP (preserva historial); se añade aviso en contexto de la cifra.

---

### C-02 — Vacío estructural: categoría `convenios` sin páginas

| Campo | Valor |
|---|---|
| **Clase** | vacío (categoría sin cobertura) |
| **Severidad** | 2 |
| **Afectados** | taxonomía `semantic/convenios/` declarada en `company.yaml`; `[[index.md]]` |
| **Alcance** | 1 estructura (categoría + mención en índice) |
| **Impacto** | 2×10 + 1 = **21** |

**Evidencia.**
`company.yaml` declara `convenios` en `entities` y en `taxonomy.semantic`. La carpeta `sim/academico/wiki/semantic/convenios/` no existe y ninguna página de la wiki tiene `type: convenio`. El índice lo reconoce explícitamente como "vacío conocido".

Esto no necesariamente es un error del conocimiento — puede reflejar que el grupo aún no tiene convenios vigentes — pero es un **vacío estructural** que el detector de LINT debe registrar y que en AUDIT se convierte en candidato de baja prioridad: si el grupo no tiene convenios, la categoría puede eliminarse del manifiesto para reducir ruido de auditoría futuro; si los tiene pero no los ingirió, hay un hueco de cobertura real.

**Diff propuesto (opción A — eliminar categoría vacía del manifiesto):**
En `company.yaml`, quitar `convenios` de `entities` y de `taxonomy.semantic`. Añadir comentario: `# eliminado AUDIT 2026-06-25: sin páginas tras 18 meses de operación`.

**Diff propuesto (opción B — mantener y documentar):**
Crear `sim/academico/wiki/semantic/convenios/README.md` con frontmatter mínimo y nota: "Sin convenios activos a 2026-06-25. Categoría reservada." Esto cierra el vacío sin perder la estructura.

---

### C-03 — Link roto: `[[metodologia-iva-2016]]` citada pero inexistente

| Campo | Valor |
|---|---|
| **Clase** | vacío (link roto / cita sin trazabilidad) |
| **Severidad** | 2 |
| **Afectados** | [[preprint-replica-rios-2023]] (campos `relations.cita` y `relations.replica`) |
| **Alcance** | 1 página + 2 campos de relación |
| **Impacto** | 2×10 + 1 = **21** |

**Evidencia.**
`[[preprint-replica-rios-2023]]` declara:
- `relations.cita: ["[[metodologia-iva-2016]]"]`
- `relations.replica: ["[[metodologia-iva-2016]]"]`

La página `metodologia-iva-2016` no existe en `sim/academico/wiki/`. El índice la menciona como vacío conocido. La página ya tiene `tag: cita-pendiente` y `confidence: 0.4`, lo que indica que INGEST reconoció el problema en su momento. El gen [[gen-cita-trazable]] establece que sin trazabilidad de fuente `confidence <= 0.4`, condición ya cumplida.

El candidato persiste porque el link roto sigue activo (no resuelto) y porque la relación `replica` apunta también a la misma fuente inexistente, lo que implica que la cadena de reproducibilidad metodológica está incompleta.

**Diff propuesto.**
En `[[preprint-replica-rios-2023]]`, sustituir los links a `[[metodologia-iva-2016]]` por referencia literal con tag de pendiente:
```yaml
relations:
  cita:
    - "metodologia-iva-2016 (cita-pendiente — fuente no ingresada)"
  replica:
    - "metodologia-iva-2016 (cita-pendiente — fuente no ingresada)"
```
O bien: crear página stub `sim/academico/wiki/semantic/papers/metodologia-iva-2016.md` con frontmatter mínimo (`confidence: 0.3`, `tag: stub-pendiente-ingerir`) para que el link no esté roto aunque la fuente no esté completa.

> **Nota de desempate vs. C-02:** ambos tienen impacto 21. Por prioridad canónica de clase (vacío = misma fila en rúbrica), se desempata por ruta alfabética: `preprint-replica-rios-2023` < `wiki/index`; C-03 va antes de C-02. Impacto idéntico, orden final en ranking: C-01 (42) > C-02 / C-03 (21, desempatar por ruta) — ver tabla resumen.

---

### C-04 — Redundancia de datasets: `enph-2022-giea` y `enph-2022-v2-giea`

| Campo | Valor |
|---|---|
| **Clase** | redundancia (near-duplicado) |
| **Severidad** | 2 |
| **Afectados** | [[enph-2022-giea]], [[enph-2022-v2-giea]] |
| **Alcance** | 2 páginas |
| **Impacto** | 2×10 + 2 = **22** |

**Evidencia.**
Ambos datasets:
- Misma fuente raw: `[[raw/enph-2022-readme]]`
- Misma descarga original: 2023-11-08
- Misma fuente DANE (ENPH 2022, módulo servicios públicos)
- `confidence: 0.85` idéntica
- `decay_rate: low` idéntica

Diferencias documentadas: `enph-2022-v2-giea` corrige codificación de 214 hogares del Chocó en `tarifa_efectiva` y excluye estrato 1 explícitamente; cubre ~26.800 hogares vs. ~28.000 del v1. Tiene relación `deriva_de: [[enph-2022-giea]]`.

La relación `deriva_de` existe y documenta el vínculo, lo que es correcto. Pero ambas tienen la misma `confidence` (0.85) a pesar de que v2 es más limpia metodológicamente, y ninguna de las dos está marcada como "canónica para GIEA-2025-03" frente a la otra. El paper publicado `giea-2025-03` cita **ambas** en `relations.cita`, lo que puede llevar a confusión sobre cuál es el dataset de reproducibilidad del resultado publicado.

**Diff propuesto.**
En `[[enph-2022-v2-giea]]`: añadir `tags: [..., canonica-giea-2025-03]` y nota: "Versión canónica para reproducir [[giea-2025-03]]. [[enph-2022-giea]] es la versión previa usada solo en [[wp-2024-07]]."
En `[[enph-2022-giea]]`: añadir nota: "Para [[giea-2025-03]] usar [[enph-2022-v2-giea]] (versión corregida). Esta versión es base histórica de [[wp-2024-07]]."
No fusionar: las versiones tienen usos distintos por paper y el historial de estimaciones es valioso.

---

### C-05 — Obsolescencia: WP supersedido sin degradación de tier

| Campo | Valor |
|---|---|
| **Clase** | regla obsoleta/deprecable → adaptado: **página con obsolescencia implícita** |
| **Severidad** | 3 |
| **Afectados** | [[wp-2024-07]] |
| **Alcance** | 1 página |
| **Impacto** | 3×10 + 1 = **31** |

**Evidencia.**
`[[wp-2024-07]]` tiene `tag: supersedido` y `confidence: 0.5`, cumpliendo las instrucciones de `gen-version-paper`. Sin embargo, permanece en `semantic/` sin ningún indicador de degradación de tier ni aviso explícito en el campo adecuado. El gen `gen-version-paper` dice: "nunca se borra porque el historial de estimaciones preliminares es valioso", pero no especifica si debe permanecer en `semantic/` o moverse a `working/` u otro tier de archivo.

La consecuencia práctica: cualquier QUERY que recorra `semantic/papers/` encuentra `wp-2024-07` sin saber de inmediato que su estimación central fue reemplazada. El riesgo se solapa parcialmente con C-01 (la nota de aviso en el cuerpo) pero es independiente: atañe al **tier y a los metadatos de estado**, no solo a la nota textual.

Nota de clasificación: la rúbrica base categoriza "regla obsoleta/deprecable" con sev 3 para genes, no para páginas wiki. Aquí aplico sev 3 por analogía (página con estado de obsolescencia no reflejado en metadatos de tier), que es mayor que "vacío" (sev 2) pero menor que "contradicción entre páginas" (sev 4). Ver Fricciones del gen más abajo.

**Diff propuesto.**
En `[[wp-2024-07]]`:
- Cambiar `tier: semantic` → `tier: working` (o añadir `tier_status: archivado`) para señalizar que ya no es conocimiento operativo vigente sino registro histórico.
- Añadir `estado: supersedido` como campo de frontmatter explícito.
- Mover físicamente el archivo a `wiki/working/papers/wp-2024-07.md` si el gen de tier lo permite, dejando un redirect o nota en `semantic/`.

---

## Tabla resumen de candidatos

| ID | Clase | Afectados | Sev | Alcance | Impacto | Diff (1 línea) |
|---|---|---|---|---|---|---|
| C-01 | contradicción entre páginas | [[wp-2024-07]], [[giea-2025-03]] | 4 | 2 | **42** | Añadir nota de discrepancia −0.8 vs −0.5 en cuerpo de [[wp-2024-07]] |
| C-05 | obsolescencia de página | [[wp-2024-07]] | 3 | 1 | **31** | Degradar tier a `working`; añadir `estado: supersedido` en frontmatter |
| C-04 | redundancia near-duplicado | [[enph-2022-giea]], [[enph-2022-v2-giea]] | 2 | 2 | **22** | Marcar [[enph-2022-v2-giea]] como canónica para [[giea-2025-03]] |
| C-02 | vacío estructural (categoría) | `convenios` en manifiesto | 2 | 1 | **21** | Eliminar `convenios` del manifiesto o crear stub con nota de cobertura |
| C-03 | vacío (link roto) | [[preprint-replica-rios-2023]] | 2 | 1 | **21** | Crear stub `metodologia-iva-2016` o sustituir link por referencia literal pendiente |

**Ranking final (top-3 por impacto; desempate C-02 vs C-03 por ruta alfabética: C-03 antes de C-02):**
1. C-01 (impacto 42)
2. C-05 (impacto 31)
3. C-04 (impacto 22)

C-02 y C-03 quedan fuera del top-3 pero documentados como candidatos para el auditor.

---

## Confidencialidad — verificación

La página `[[revision-par-giea-2025-03]]` tiene `sensibilidad: confidencial`. En todos los candidatos anteriores:
- Se referencia únicamente por `[[link]]` y por campos de frontmatter (`sensibilidad`, `type`, `deriva_de`).
- No se transcribe la identidad del revisor, ni la recomendación inicial, ni ningún contenido sensible del cuerpo de la página.
- La página fue leída internamente para verificar que no genera ningún candidato de auditoría por sí misma (relaciones limpias, link `deriva_de` a [[giea-2025-03]] válido, frontmatter completo). Dicha lectura interna no se reproduce aquí.

Confirmado: cero valores confidenciales transcritos.

---

## Fricciones del gen (afinación)

### F-1. "supersede / working-paper → publicado": ¿es limpiamente `obsolescencia` o necesita clase propia?

La rúbrica actual lista como clase de sev 3 "regla obsoleta/deprecable" refiriéndose a genes del genoma, no a páginas wiki. Un WP supersedido no es exactamente una "regla obsoleta" — es conocimiento que conserva valor histórico pero cuya estimación central fue superada. Aplicar sev 3 por analogía (C-05) es razonable pero forzado.

**Propuesta de afinación:** añadir una clase propia en la rúbrica:

| Clase | severidad |
|---|---|
| conocimiento supersedido sin degradación de tier/estado | 3 |

Esto también haría que `gen-version-paper` pudiera especificar explícitamente qué metadatos deben cambiar al marcar un WP como supersedido (p. ej. `tier_status: archivado`, `estado: supersedido`), en lugar de solo bajar `confidence` y añadir `tag`.

Clasificación según rúbrica actual: C-05 se asienta en sev 3 por analogía con "regla obsoleta/deprecable". No hay clase mejor. El gen debería aclarar este caso para que el detector sea determinista.

---

### F-2. ¿El "vencido académico" (supersedido/retractado) encaja en `vencido-seguridad` (sev 5)?

La rúbrica tiene sev 5 para "info vencida en dominio de seguridad". En un entorno académico, un paper retractado o una metodología oficialmente supersedida puede tener consecuencias serias (citación de resultados incorrectos, mala política pública basada en estimaciones obsoletas), pero no es equivalente a un riesgo de seguridad física o de infraestructura.

Aplicar sev 5 a un WP supersedido en economía sería **demasiado alto** fuera de contextos de salud, seguridad física o normativa regulatoria. Un artículo económico retractado merece sev 3-4 (contradicción entre páginas + estado incorrecto), no sev 5.

**Propuesta de afinación:** la descripción de sev 5 debería decir explícitamente "dominio de seguridad física, salud, o cumplimiento regulatorio con consecuencias legales" para no inflar la severidad en dominios académicos estándar. El gen podría recibir un campo `domain_context` en su rúbrica para que el operador declare si el dominio es de alta consecuencia.

---

### F-3. La contradicción WP vs. publicado: ¿candidato de sev 4 o de sev 5?

C-01 (elasticidad −0.8 vs −0.5) fue clasificado como "contradicción entre páginas wiki" (sev 4). Podría argumentarse que, si el grupo usa estas estimaciones para asesorar política pública, la contradicción tiene consecuencias de dominio de alta consecuencia y merece sev 5. Sin embargo, la rúbrica reserva sev 5 para "dominio de seguridad", y la naturaleza del defecto es una contradicción entre versiones — no una información vencida per se. Sev 4 es correcto según la rúbrica actual.

Fricciones: la rúbrica no tiene un mecanismo para elevar la severidad por contexto de uso (política pública vs. investigación básica). Una variante mejorada del gen podría permitir que el manifiesto declare `impact_domain: policy` para elevar la severidad base de contradicciones en 1 punto.

---

### F-4. Redundancia de datasets con relación `deriva_de` declarada: ¿es realmente redundancia?

`enph-2022-v2-giea` declara `deriva_de: [[enph-2022-giea]]` — lo que significa que el genoma ya reconoce la relación entre ambos. El detector de CONSOLIDATE señalaría near-duplicado, pero la relación `deriva_de` es exactamente el mecanismo que el gen usa para documentar versiones de dataset. La "redundancia" aquí no es un defecto de gestión del conocimiento sino una consecuencia legítima de tener versiones distintas del mismo origen.

La fricción: el detector de near-duplicados debería **exemptuar** pares donde ya existe una relación `deriva_de` o `supersede` declarada, o al menos bajar la severidad a 1 (advertencia). Actualmente no hay ese matiz en el gen. C-04 sigue siendo candidato válido porque la ausencia de marcado canónico es real, pero su clasificación como "redundancia" puede ser confusa con la relación estructural ya presente.

---

*Fin de 10-maker.md. Listo para paso siguiente: rol Auditor lee este archivo + 00-snapshot.md y escribe 20-auditor.md.*
