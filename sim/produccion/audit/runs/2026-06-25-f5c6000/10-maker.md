---
run_id: 2026-06-25-f5c6000
scenario: produccion
fecha: 2026-06-25
rol: maker
gen_auditoria_version: 1
insumos: ["00-snapshot.md", "genome/genes/gen-auto-auditoria.md"]
---

# 10 — Maker: candidatos de defecto

Insumos: `00-snapshot.md` + `gen-auto-auditoria.md` (v1).  
Detectores aplicados: LINT (huérfanos, vencidos, campos fuera de esquema, links rotos),
CONSOLIDATE (duplicados), redundancia/obsolescencia de genoma.  
Hoy: 2026-06-25.

---

## Candidatos

### C1 — SOP-MTTO-01: brecha documentada pero no corregida en el SOP

**Clase:** contradicción entre página wiki y conocimiento ingerido  
**Clase canónica (rúbrica):** contradicción entre páginas wiki (severidad 4)

**Afectados:**
- [[SOP-MTTO-01-preventivo-prensas]] (el SOP defectuoso)
- [[MTTO-PR-200-2026-06]] (evento que detectó la brecha)
- [[NCR-2026-031]] (NCR cuya causa raíz es esa brecha)
- [[PR-200-prensa]] (máquina cuyo estado futuro depende de si el checklist se corrige)

**Evidencia:**

`SOP-MTTO-01` tiene como checklist actual:
```
1. Engrase de guías.
2. Revisión de presión hidráulica (rango OK: 200–215 bar).
3. Inspección visual de troquel.
```
Y en la sección "Brecha detectada" del mismo SOP dice:
> "MTTO-PR-200-2026-06 mostró que el checklist NO incluye la verificación del tope de
> altura del troquel, causa raíz de NCR-2026-031. Acción propuesta: añadir el punto 4…"

El raw `MTTO-PR-200-2026-06.md` confirma:
> "Recomendación: añadir ese punto al checklist de MP de prensas de estampado."

Resultado: el SOP describe su propio fallo (la brecha) pero el checklist operativo NO ha
sido actualizado. Cualquier técnico que siga el checklist del cuerpo del SOP reproducirá la
causa raíz de NCR-2026-031. La contradicción es interna a la misma página: el cuerpo
describe los pasos vigentes sin el punto 4, mientras la sección "Brecha" lo exige.

En términos de safety de proceso (mantenimiento de máquina), este SOP es análogo a un
documento de seguridad operacional: la brecha es re-ejecutable en producción.

**Severidad:** 4 (contradicción entre páginas wiki; agravada por implicación de mantenimiento
de seguridad de proceso — ver nota en Fricciones sobre slot de "vencido-seguridad")  
**Alcance:** 4 páginas afectadas  
**Impacto:** 4×10 + 4 = **44**

**Diff (one-line):**
```
SOP-MTTO-01 checklist: añadir punto 4 "Verificar y registrar cota del tope de
altura del troquel con galga patrón; registrar valor medido en hoja de MTTO."
```

---

### C2 — company.yaml no declara `relation_types`: 14 verbos de dominio son huérfanos semánticos

**Clase:** vacío (categoría declarada sin cobertura / campo de esquema ausente)  
**Clase canónica (rúbrica):** vacío — link roto / categoría sin cobertura (severidad 2)

**Afectados:**
- `sim/produccion/company.yaml` (manifiesto ONBOARD que debería declarar `relation_types`)
- Todas las páginas wiki que usan verbos fuera del núcleo: [[LOTE-SM45-2606]], [[OP-2026-0417]],
  [[NCR-2026-031]], [[MTTO-PR-200-2026-06]], [[PR-200-prensa]], [[soporte-motor-SM45]],
  [[L1-estampado]], [[AceroNorte]], [[SOP-CAL-03-inspeccion-dimensional]],
  [[SOP-MTTO-01-preventivo-prensas]]

**Evidencia:**

`gen-frontmatter-obligatorio` (referenciado como gen base) declara que `relations` tiene un
núcleo `{usa, depende_de, contradice, reemplaza}`. Las páginas del sandbox usan 14 verbos
adicionales de dominio (`producido_en`, `bajo_orden`, `de_producto`, `insumo_de`,
`afectado_por`, `afecta_a`, `viola`, `causa_en`, `mitiga_con`, `ubicada_en`, `produjo_lote`,
`intervenida_por`, `mantiene_a`, `resuelve`) que NO están declarados en `relation_types`
del `company.yaml` (el bloque `relation_types` no existe en el manifiesto).

`regresion.md` confirma: "el manifiesto del sandbox `sim/produccion/company.yaml` NO declara
el bloque `relation_types`… Con el gen v3, LINT los marcaría TODOS como verbos huérfanos."

Un futuro LINT contra el genoma-applied marcaría cada relación tipada de esos 14 verbos como
"verbo huérfano semántico", degradando la auditabilidad del grafo completo de trazabilidad.

**Severidad:** 2 (vacío)  
**Alcance:** 10 páginas wiki + 1 manifiesto = 11  
**Impacto:** 2×10 + 11 = **31**

**Diff (one-line):**
```
company.yaml: añadir bloque relation_types con los 14 verbos de dominio usados
(producido_en, bajo_orden, de_producto, insumo_de, afectado_por, afecta_a, viola,
causa_en, mitiga_con, ubicada_en, produjo_lote, intervenida_por, mantiene_a, resuelve).
```

---

### C3 — NCR-2026-031, MTTO-PR-200-2026-06 y OP-2026-0417 no declaran `clase:` exigido por gen-clase-temporal

**Clase:** campo fuera de esquema / vacío de campo obligatorio  
**Clase canónica (rúbrica):** vacío — categoría sin cobertura (severidad 2)

**Afectados:**
- [[NCR-2026-031]] (tipo `fuente`, decay high, evento fechado — falta `clase: evento`)
- [[MTTO-PR-200-2026-06]] (tipo `fuente`, decay high, evento fechado — falta `clase: evento`)
- [[OP-2026-0417]] (tipo `fuente`, decay medium, evento fechado — falta `clase: evento`)

Potencialmente también: [[LOTE-SM45-2606]] (debería declarar `clase: estable`)

**Evidencia:**

`gen-clase-temporal` (genome-applied del genoma nuevo, referenciado en `regresion.md` como
resuelto para F2) exige que toda página de `wiki/` declare `clase: estable | evento`. Las
páginas de evento fechado (NCR, MTTO, OP) ya tienen `decay_rate: high|medium` y en prosa
dicen "evento fechado", pero el campo literal `clase:` no aparece en ningún frontmatter del
sandbox.

`regresion.md` lo confirma: "Mejora menor: añadir el campo explícito `clase:` al frontmatter
de NCR y MTTO para que LINT lo lea sin inferir."

Sin el campo `clase:` explícito, LINT (que opera sobre frontmatter) no puede distinguir
automáticamente entre estable y evento: debe inferirlo de tags o decay_rate, que son campos
menos formales y más ambiguos.

**Nota:** este candidato es de menor severidad (campo faltante que LINT podría inferir con
heurística de fallback), pero es real: la regla del gen exige declaración explícita.

**Severidad:** 2 (vacío de campo obligatorio)  
**Alcance:** 3 páginas afectadas directamente (+ 1 potencial)  
**Impacto:** 2×10 + 3 = **23**

**Diff (one-line):**
```
Frontmatter de NCR-2026-031, MTTO-PR-200-2026-06 y OP-2026-0417: añadir campo
`clase: evento`; a LOTE-SM45-2606 y entidades estables: añadir `clase: estable`.
```

---

### C4 — MTTO-PR-200-2026-06: `relations.usa: [[AceroNorte]]` usa un verbo de núcleo con semántica incorrecta

**Clase:** campo fuera de esquema (verbo de núcleo aplicado con semántica incorrecta)  
**Clase canónica (rúbrica):** vacío / campo fuera de esquema (severidad 2)

**Afectados:**
- [[MTTO-PR-200-2026-06]]
- [[AceroNorte]]

**Evidencia:**

El frontmatter de `MTTO-PR-200-2026-06` usa:
```yaml
relations:
  usa:
    - "[[AceroNorte]]"
```
El cuerpo del documento aclara que AceroNorte es el proveedor del repuesto TRQ-SM45-A usado
en la intervención. La relación semántica correcta sería `consumio_repuesto_de` o bien
`insumo_de`, no `usa` (que en el núcleo del gen-frontmatter tiene la connotación de
dependencia de herramienta/recurso genérica). Usar `usa` aquí oculta que AceroNorte actúa
como proveedor de utillaje, no simplemente como dependencia.

Aunque sin `relation_types` declarado (C2) todos los verbos alternativos también serían
huérfanos, este es un uso observable del verbo de núcleo con semántica incorrecta: un LINT
que evaluase la consistencia semántica entre el verbo y el tipo de entidad destino lo
marcaría.

**Severidad:** 2  
**Alcance:** 2 páginas  
**Impacto:** 2×10 + 2 = **22**

**Diff (one-line):**
```
MTTO-PR-200-2026-06 relations: mover [[AceroNorte]] de `usa:` a un verbo semántico
adecuado (p.ej. `insumo_de:` o `consumio_repuesto_de:`) una vez declarado en relation_types.
```

---

## Tabla resumen de candidatos

| id | clase (rúbrica) | sev | alcance | impacto | página(s) principal(es) |
|---|---|---|---|---|---|
| C1 | contradicción entre páginas wiki | 4 | 4 | **44** | [[SOP-MTTO-01-preventivo-prensas]] |
| C2 | vacío (categoría sin cobertura) | 2 | 11 | **31** | company.yaml + 10 páginas wiki |
| C3 | vacío (campo obligatorio ausente) | 2 | 3 | **23** | [[NCR-2026-031]], [[MTTO-PR-200-2026-06]], [[OP-2026-0417]] |
| C4 | campo fuera de esquema | 2 | 2 | **22** | [[MTTO-PR-200-2026-06]] |

**Ranking por impacto:** C1 > C2 > C3 > C4

**Top-3 para el auditor:** C1, C2, C3.  
C4 es el de menor impacto; queda como candidato de reserva si el auditor descarta alguno del top-3.

---

## Verificación de confidencialidad

Ninguna página del sandbox declara `sensibilidad: confidencial`. No se omitió ni redactó
ningún valor por esta causa.

---

## Fricciones del gen (afinación)

### Fricción 1 — La rúbrica no tiene slot para "entidad con estado obsoleto / inconsistente"

El gen-auto-auditoria v1 tiene 6 clases en la rúbrica de severidad:

| Clase | sev |
|---|---|
| contradicción entre genes activos | 5 |
| info vencida en dominio de seguridad | 5 |
| contradicción entre páginas wiki | 4 |
| regla obsoleta/deprecable | 3 |
| vacío (link roto / categoría sin cobertura) | 2 |
| redundancia (duplicado) | 2 |

**Problema observado:** en este escenario aparecen dos patrones que no encajan limpiamente
en ninguna clase:

**a) Estado de máquina potencialmente desactualizado (entidad-con-estado obsoleta)**

`gen-estado-maquina` exige que el campo `estado` de la máquina se actualice in-place con
cada intervención. Si un evento de mantenimiento ingresado no activa la actualización de
`estado` en la entidad-máquina, la máquina tendría un `estado` obsoleto (p. ej., sigue como
`operativa` cuando en realidad está `en-mantenimiento`). Este defecto NO es una
"contradicción entre páginas wiki" en sentido estricto (no hay dos páginas que digan cosas
opuestas entre sí), ni es un "vencido" (no hay `valido_hasta`), ni es un "huérfano".

En este sandbox concreto el estado SÍ está actualizado correctamente (PR-200 dice `operativa`
y hay un MTTO que lo respalda). Pero el detector de la rúbrica no tiene una clase "estado de
entidad inconsistente con su historial de eventos" que lo capturaría sistemáticamente. Si la
PR-200 dijera `en-mantenimiento` sin ningún MTTO abierto que lo respalde, el auditor actual
lo asimilaría a "contradicción entre páginas wiki" (sev 4) o lo dejaría sin clase canónica.

**b) NCR abierta vencida (open NCR past-due)**

Si una NCR tuviera `estado: abierta` y la fecha de detección fuera hace más de N días sin
disposición documentada, esto sería un defecto de proceso crítico en manufactura (calidad,
seguridad). La rúbrica podría absorberlo como "info vencida en dominio de seguridad" (sev 5)
si el auditor argumenta que una NCR de calidad es "dominio de seguridad", pero el gen no lo
dice explícitamente: el ejemplo del gen apunta a SOPs de seguridad física, no a no-conformidades.
En este sandbox NCR-2026-031 está cerrada, así que el caso no se activa, pero es latente para
cualquier escenario con NCRs abiertas.

**Recomendación de afinación (para EVOLVE posterior):**
Añadir una clase a la rúbrica:

| Clase | sev propuesta |
|---|---|
| entidad con estado inconsistente con su historial de eventos | 4 |
| NCR / no-conformidad abierta vencida (past-due) | 5 (equiparar a "vencido en dominio de seguridad") |

Esta adición requeriría subir `version` del gen-auto-auditoria y pasar por
[[gen-compuerta-mutacion]].

### Fricción 2 — La clase "info vencida en dominio de seguridad" (sev 5) no define "dominio de seguridad"

El gen no aclara si SOPs de mantenimiento de maquinaria o NCRs de calidad de proceso
(que pueden impactar seguridad del producto en el cliente) cuentan como "dominio de
seguridad". En manufactura, un SOP de mantenimiento preventivo de prensas hidráulicas
claramente es operacionalmente crítico (riesgo de accidente o de producto no conforme que
llega al cliente). El auditor tiene que decidir sin criterio explícito.

En C1 (SOP-MTTO-01 con checklist desactualizado) hay un argumento para sev 5 en lugar de
sev 4, pero la rúbrica no resuelve la ambigüedad. Se optó conservadoramente por sev 4
(contradicción entre páginas wiki) para no sobreestimar.

**Recomendación:** el gen debería enunciar los dominios que califican como "seguridad":
p.ej. "SOPs de operación de maquinaria, SOPs de LOTO, registros de calibración y NCRs de
severidad `critica` en productos que van a cliente".

### Fricción 3 — El detector de "entidad con estado" no está en LINT ni en CONSOLIDATE

El gen-auto-auditoria reutiliza LINT + CONSOLIDATE más un detector nuevo de redundancia de
genoma. Pero la verificación "toda entidad con campo `estado` debe tener al menos un evento
de respaldo que justifique el `estado` actual" no está definida en gen-lint ni en
gen-consolidate. Es un detector nuevo específico de `gen-estado-maquina` que la
auto-auditoría tendría que implementar como extensión de LINT o como cuarto detector
independiente. El gen no lo prevé explícitamente.

**En este sandbox:** el chequeo se hizo manualmente (PR-200 `estado: operativa` +
`MTTO-PR-200-2026-06` que la respalda → consistente). No hay falso negativo aquí, pero si
hubiera decenas de máquinas sería indetectable sin automatización del detector.

### Fricción 4 — `proximo_preventivo` como vigencia auditable

`PR-200-prensa` tiene `proximo_preventivo: 2026-07-19`. Hoy es 2026-06-25; no está vencido.
Pero si hoy fuera 2026-07-20 y la máquina siguiera como `operativa` sin un MTTO nuevo, ese
campo debería disparar una advertencia de tipo "vencido en dominio de seguridad/mantenimiento".

El gen-vigencia-temporal opera sobre `valido_hasta`, no sobre campos de dominio como
`proximo_preventivo`. Para auditarlo habría que: (a) enseñar a LINT a tratar
`proximo_preventivo` como alias de `valido_hasta` para entidades `type: maquina`, o (b) pedir
en el onboard que ese campo se llame `valido_hasta` directamente. Ninguna de las dos está
definida actualmente.

**En este sandbox no es defecto activo** (fecha futura), pero el patrón es real y repetible
en cualquier escenario de mantenimiento industrial.
