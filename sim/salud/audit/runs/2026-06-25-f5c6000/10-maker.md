---
run_id: 2026-06-25-f5c6000
scenario: salud
role: maker
date: 2026-06-25
gen_version: gen-auto-auditoria v1
insumos: [00-snapshot.md, gen-auto-auditoria.md]
candidatos: 3
---

# 10-maker — Candidatos de auditoría (Clínica Vida Serena)

> Pasada Maker. Insumos únicos: `00-snapshot.md` + `gen-auto-auditoria.md`.
> El Auditor NO debe leer este archivo junto con la memoria de sesión del Maker.

---

## CAND-01 — Protocolo clínico vencido sin reemplazo (dominio de seguridad)

| campo | valor |
|---|---|
| **clase** | info vencida en dominio de seguridad |
| **severidad** | 5 |
| **alcance** | 5 páginas afectadas |
| **impacto** | 55 |
| **afectados** | [[protocolo-anafilaxia]] (página vencida directa); [[anafilaxia]] (`tratada_segun` apunta a protocolo vencido); [[adrenalina]] (`indicado_por` apunta a protocolo vencido); [[PAC-7731]] (`segun_protocolo` apunta a protocolo vencido); [[INC-2026-014]] (`afecta_a` protocolo vencido, estado: abierto) |

**Evidencia:**

- `sim/salud/wiki/procedural/protocolos-clinicos/protocolo-anafilaxia.md`
  - campo `valid_until: 2026-03-01` → vencido hace **116 días** respecto a 2026-06-25
  - campo `status: vencido`
  - campo `reemplaza: []` → sin versión v4 publicada
  - campo `confidence: 0.55` (degradado)
- `sim/salud/wiki/semantic/patologias/anafilaxia.md`
  - `relations.tratada_segun: ["[[protocolo-anafilaxia]]"]` → apunta a documento vencido
  - nota inline: `⚠️ (vigente solo hasta 2026-03-01)` — la página reconoce el problema pero no lo resuelve
- `sim/salud/wiki/semantic/farmacos/adrenalina.md`
  - `relations.indicado_por: ["[[protocolo-anafilaxia]]"]` → única fuente de indicación es el protocolo vencido
- `sim/salud/wiki/semantic/pacientes/PAC-7731.md` — referenciada solo por [[link]]/id según regla de confidencialidad
  - `relations.segun_protocolo: ["[[protocolo-anafilaxia]]"]` — campo/id únicamente
- `sim/salud/wiki/semantic/incidentes/INC-2026-014.md`
  - `relations.afecta_a: ["[[protocolo-anafilaxia]]"]`; `estado: abierto` — incidente activo no cerrado
  - acción correctiva 2 ("acelerar publicación v4") sin fecha de cierre ni responsable asignado

**Diff propuesto:**

```diff
# wiki/procedural/protocolos-clinicos/protocolo-anafilaxia.md
- status: vencido          # mantener hasta que exista v4
+ # ACCIÓN REQUERIDA: publicar protocolo-anafilaxia-v4.md
+ # con valid_until >= 2027-03-01, enlazando reemplaza: [[protocolo-anafilaxia]] (v3)
+ # y marcando este documento status: deprecado tras publicar v4

# wiki/semantic/incidentes/INC-2026-014.md  
- # acciones correctivas sin fecha límite ni responsable
+ responsable_accion_2: "director-medico"
+ fecha_limite_accion_2: "2026-07-31"    # propuesta; requiere aprobación
```

> NOTA: La modificación del campo `status` de `vencido` a `deprecado` en protocolo-anafilaxia.md
> es posterior a la publicación de v4; el diff aquí documenta la hoja de ruta, no el cambio inmediato.
> El Maker NO aplica nada — propone solo.

**Score:** `impacto = 5×10 + 5 = 55`

---

## CAND-02 — Contradicción de relación tipada en la tríada clínica (anafilaxia ↔ adrenalina)

| campo | valor |
|---|---|
| **clase** | contradicción entre páginas wiki |
| **severidad** | 4 |
| **alcance** | 2 páginas afectadas |
| **impacto** | 42 |
| **afectados** | [[anafilaxia]], [[adrenalina]] |

**Evidencia:**

- `sim/salud/wiki/semantic/farmacos/adrenalina.md`
  - `relations.tratamiento_de: ["[[anafilaxia]]"]`
  - Interpretación per `gen-trazabilidad-clinica`: correcto — el gen establece que **el fármaco** lleva `tratamiento_de` → patología.

- `sim/salud/wiki/semantic/patologias/anafilaxia.md`
  - `relations.tratamiento_de: ["[[adrenalina]]"]`
  - Interpretación per `gen-trazabilidad-clinica`: **incorrecto** — la patología no debería llevar `tratamiento_de`; esa relación pertenece al fármaco. La patología debería llevar únicamente `tratada_segun` (→ protocolo). Al tener `tratamiento_de` apuntando al fármaco, el grafo expresa la misma relación en ambas direcciones con el mismo tipo de relación, creando ambigüedad semántica: una query sobre `tratamiento_de` en patologías devolvería el fármaco, pero la query sobre `tratamiento_de` en fármacos también devolvería la patología — la flecha semántica va en sentidos contrarios para el mismo tipo de relación.

- Relaciones definidas por `gen-trazabilidad-clinica` (canónicas):
  - fármaco → `tratamiento_de` → patología ✅ (adrenalina.md cumple)
  - patología → `tratada_segun` → protocolo ✅ (anafilaxia.md cumple)
  - patología → [sin `tratamiento_de`] — `anafilaxia.md` tiene el campo extra incorrecto ❌

**Diff propuesto:**

```diff
# wiki/semantic/patologias/anafilaxia.md — frontmatter relations
  relations:
    usa: []
    depende_de: []
    tratada_segun: ["[[protocolo-anafilaxia]]"]
-   tratamiento_de: ["[[adrenalina]]"]   # relación invertida; pertenece a adrenalina.md
    contradice: []
    reemplaza: []
```

**Score:** `impacto = 4×10 + 2 = 42`

---

## CAND-03 — Vacío de cobertura: entidades del manifiesto sin página wiki

| campo | valor |
|---|---|
| **clase** | vacío (link roto / categoría sin cobertura) |
| **severidad** | 2 |
| **alcance** | 7 entidades sin página (ver detalle) |
| **impacto** | 27 |
| **afectados** | (links rotos potenciales: [[sepsis-adulto]], [[dolor-toracico]], [[sepsis]], [[neumonia-adquirida-comunidad]], [[amoxicilina-clavulanico]], [[noradrenalina]], [[dr-nunez-mi]]) |

**Evidencia:**

`company.yaml` declara las siguientes entidades; ninguna tiene página en `sim/salud/wiki/`:

| entidad | categoría | referenciada en wiki? |
|---|---|---|
| sepsis-adulto | protocolos | sí — INC-2026-014 menciona "Auditar otros protocolos (…sepsis)" |
| dolor-toracico | protocolos | sí — INC-2026-014 menciona "dolor torácico" |
| sepsis | patologias | no |
| neumonia-adquirida-comunidad | patologias | no |
| amoxicilina-clavulanico | farmacos | no |
| noradrenalina | farmacos | no |
| dr-nunez-mi | profesionales | no |

`INC-2026-014.md` acción 4: "Auditar otros protocolos con revisión vencida (dolor torácico, sepsis)" — sugiere que esos protocolos existen como fuentes pero no tienen página wiki. Si `valid_until` de esos protocolos también está vencido, el impacto del CAND-01 se amplificaría; sin sus páginas no es auditable.

La ausencia de páginas para amoxicilina-clavulanico y noradrenalina viola `gen-trazabilidad-clinica` (toda ficha de fármaco debe enlazar al menos una patología o protocolo; sin la ficha, la regla no puede cumplirse ni verificarse).

**Diff propuesto:**

```diff
# Crear (INGEST o ONBOARD) las siguientes páginas con frontmatter mínimo:
+ wiki/semantic/patologias/sepsis.md            (decay_rate: low, tratada_segun→[[sepsis-adulto]])
+ wiki/semantic/patologias/neumonia-adquirida-comunidad.md
+ wiki/procedural/protocolos-clinicos/protocolo-sepsis-adulto.md   (valid_until: ? — auditar)
+ wiki/procedural/protocolos-clinicos/protocolo-dolor-toracico.md  (valid_until: ? — auditar)
+ wiki/semantic/farmacos/amoxicilina-clavulanico.md
+ wiki/semantic/farmacos/noradrenalina.md
+ wiki/semantic/profesionales/dr-nunez-mi.md
# Nota: los protocolos de sepsis y dolor torácico también requieren verificación de valid_until
# (posible CAND-01 adicional si están vencidos — no auditable hasta que existan las páginas).
```

**Score:** `impacto = 2×10 + 7 = 27`

---

## Tabla resumen de candidatos

| id | clase | severidad | alcance | impacto | afectados (links) |
|---|---|---|---|---|---|
| CAND-01 | info vencida en dominio de seguridad | 5 | 5 | **55** | [[protocolo-anafilaxia]], [[anafilaxia]], [[adrenalina]], [[PAC-7731]], [[INC-2026-014]] |
| CAND-02 | contradicción entre páginas wiki | 4 | 2 | **42** | [[anafilaxia]], [[adrenalina]] |
| CAND-03 | vacío (link roto / categoría sin cobertura) | 2 | 7 | **27** | [[sepsis-adulto]], [[dolor-toracico]], [[sepsis]], [[neumonia-adquirida-comunidad]], [[amoxicilina-clavulanico]], [[noradrenalina]], [[dr-nunez-mi]] |

Ranking (desempate por impacto → prioridad de clase → ruta): CAND-01 > CAND-02 > CAND-03.

No se detectaron:
- Contradicciones entre genes activos (los 3 genes tienen triggers disjuntos: datos de paciente / protocolos / relaciones fármaco-patología).
- Redundancia/duplicado entre páginas (una sola página por entidad).
- Reglas obsoletas/deprecables en genome-applied.

---

## Fricciones del gen (afinación)

### ¿La clase "info vencida en dominio de seguridad" (severidad 5) captura correctamente un protocolo clínico vencido?

**Sí, y encaja bien — pero la rúbrica infravalora el alcance real.**

La clase fue diseñada pensando en un genoma de documentación empresarial genérica. En el dominio clínico, un protocolo vencido tiene dos dimensiones de riesgo que la rúbrica actual colapsa en una sola:

1. **Riesgo clínico directo** (paciente): si el protocolo hubiera cambiado la dosis de adrenalina, el incidente habría sido grave. La severidad 5 es apropiada — pero solo captura el defecto en [[protocolo-anafilaxia]] como página, no en las páginas satélite que lo referencian sin advertencia.

2. **Riesgo documental de propagación**: [[anafilaxia]], [[adrenalina]] y [[PAC-7731]] referencian el protocolo vencido como si fuera vigente (la relación tipada no tiene campo de `status` de la referencia). Un QUERY que navega el grafo puede servir información con confianza indebida. La rúbrica actual computa `alcance` como "páginas afectadas" — correcto — pero el **impacto 55** no diferencia entre "la página vencida en sí" (riesgo directo) y "las páginas que la citan" (propagación).

**Propuesta de afinación (TUNING NOTE):**
- Para dominios de seguridad crítica (salud, aviación, farmacia), considerar escalar severidad a **6** cuando existe al menos un incidente registrado y abierto (`INC-2026-014.estado: abierto`) vinculado al defecto, o cuando `decay_rate: high` del documento vencido lo requiera.
- Alternativamente: incluir en `alcance` las páginas que referencian el documento vencido en relaciones tipadas de primer nivel (como ya hace este Maker) — y documentarlo explícitamente en el gen como "alcance de propagación".

### ¿Debería el alcance incluir páginas/incidentes que referencian el protocolo vencido?

**Sí — y este Maker lo incluyó así, pero el gen es ambiguo.**

El gen dice `alcance = nº de páginas/genes afectados` sin definir "afectado". Este Maker interpretó "afectado" como: la página defectuosa + cualquier página con una relación tipada de primer nivel hacia ella que no advierte la caducidad. Esta interpretación es razonable y produce el alcance = 5 para CAND-01.

Sin embargo, [[PAC-7731]] (confidencial) también apunta al protocolo vencido vía `segun_protocolo`. La rúbrica no da instrucción sobre cómo contar páginas confidenciales en el alcance — el Maker las incluye en el conteo (por id, no por contenido) pero esto no está especificado en el gen.

**Propuesta de afinación:** el gen debería aclarar que las páginas `confidencial: true` se cuentan en el alcance (incrementando la severidad del impacto) pero su evidencia se expresa solo por [[link]]/id + campo, nunca por valor sensible. Actualmente, la regla de confidencialidad de evidencia está documentada, pero la inclusión de páginas confidenciales en el *conteo de alcance* no.

### Relación `usa` vs `indica` en el mismo protocolo (CAND-no-generado)

`protocolo-anafilaxia.md` tiene `usa: ["[[adrenalina]]"]` y `indica: ["[[adrenalina]]"]` simultáneamente. Según `gen-trazabilidad-clinica`, el verbo canónico es `indica`; `usa` es un verbo genérico sin tipo semántico preciso. El gen no prohíbe explícitamente coexistencia de relaciones, por lo que esto es ambigüedad semántica pero no contradicción auditable. **No se generó candidato** porque el gen no lo clasifica como defecto; pero sería una mejora de consistencia que el gen debería explicitar: ¿`usa` es un sinónimo redundante de `indica` y debería prohibirse cuando ya existe `indica`?

### Sobre la separación maker ≠ auditor en modo TUNING

En modo TUNING/sandbox, el mismo agente juega ambos roles en sesiones diferentes. El canon portable (hand-off en disco) funciona bien: `10-maker.md` es el único insumo del Auditor además del snapshot. Sin embargo, el gen no especifica qué hacer cuando el sandbox no tiene SHA de git (`sim/` no tiene su propio repo). Este Maker usó `2026-06-25-f5c6000` como run-id dado por el orquestador; el gen debería aclarar el comportamiento en sandboxes sin SHA.
