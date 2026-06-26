---
run_id: 2026-06-25-f5c6000
role: maker
date: 2026-06-25
gene_version: gen-auto-auditoria v1
candidates: 5
---

# Candidatos MAKER — AUDIT 2026-06-25-f5c6000

Método aplicado: esqueleto pass (frontmatter + relaciones de todas las páginas) → drill-down
sobre páginas flaggeadas. Detectores: LINT (vencidos, huérfanos, campos fuera de esquema),
CONSOLIDATE (near-duplicados), Genoma (triggers solapados, reglas subsumidas). Confidencialidad:
ninguna página marcada `sensibilidad: confidencial` en este sandbox.

---

## C1 — Precio vencido (info vencida, dominio comercial sensible)

| Campo | Valor |
|---|---|
| **clase** | info vencida en dominio de seguridad |
| **severidad** | 5 |
| **páginas afectadas** | [[precio-vertice-PROP-2026-038]], [[propuesta-vertice-PROP-2026-038]], [[lead-inmobiliaria-vertice]] |
| **alcance** | 3 |
| **impacto** | 53 |

**Evidencia**: `precio-vertice-PROP-2026-038` tiene `valido_hasta: 2026-06-14`; hoy es
2026-06-25 → vencido hace 11 días. El campo `decay_rate: high` está bien declarado, y la
página incluye una advertencia inline, pero la relación `define_precio` desde
`propuesta-vertice-PROP-2026-038` no repite la advertencia; ni `lead-inmobiliaria-vertice`
(estado `en-negociacion`) lleva ningún campo que propague el vencimiento. Un agente que
consulte la propuesta sin leer el precio podría citar cifras expiradas. La clase correcta
según la rúbrica es "info vencida en dominio de seguridad" porque los precios erróneos son
el riesgo comercial más alto de esta base.

**Diff propuesto**:
```yaml
# propuesta-vertice-PROP-2026-038.md — añadir al frontmatter:
advertencia: "precio define_precio [[precio-vertice-PROP-2026-038]] vencido 2026-06-14; requiere recotización."

# lead-inmobiliaria-vertice.md — añadir al frontmatter:
advertencia: "propuesta [[propuesta-vertice-PROP-2026-038]] con precio vencido 2026-06-14; negociar precio antes de cerrar."
```

---

## C2 — Follow-up vencido con estado operativo inconcluso

| Campo | Valor |
|---|---|
| **clase** | info vencida en dominio de seguridad |
| **severidad** | 5 |
| **páginas afectadas** | [[followup-vertice-2026-05-16]], [[lead-inmobiliaria-vertice]] |
| **alcance** | 2 |
| **impacto** | 52 |

**Evidencia**: `followup-vertice-2026-05-16` tiene `fecha_objetivo: 2026-05-16` y
`estado: vencido`; hoy 2026-06-25 hay 40 días de retraso. La página sabe que está vencida
(lo dice en la prosa), pero `lead-inmobiliaria-vertice` conserva `estado: en-negociacion`
y `tiene_followup: ["[[followup-vertice-2026-05-16]]"]` sin ningún campo que indique que
la ventana de decisión (junta 2026-05-19) ya pasó. Un QUERY sobre el lead devuelve un estado
aparentemente activo cuando en realidad el hito comercial clave expiró. La clase aplica como
"info vencida en dominio de seguridad" porque el estado activo del lead es dato operativo
crítico para el equipo de ventas.

**Diff propuesto**:
```yaml
# lead-inmobiliaria-vertice.md — modificar campo estado y añadir advertencia:
estado: en-negociacion-sin-followup
advertencia: "follow-up [[followup-vertice-2026-05-16]] vencido 2026-05-16; junta decisión 2026-05-19 ya pasó. Validar si lead sigue activo o se perdió."
```

---

## C3 — Vacío de categoría: `onboarding-cliente` sin ninguna página

| Campo | Valor |
|---|---|
| **clase** | vacío (categoría sin cobertura) |
| **severidad** | 2 |
| **páginas afectadas** | `wiki/procedural/onboarding-cliente/` (carpeta vacía) |
| **alcance** | 1 |
| **impacto** | 21 |

**Evidencia**: `company.yaml` → `taxonomy.procedural` declara `[sops-ventas, onboarding-cliente]`.
La carpeta `sops-ventas` tiene `sop-calificacion-y-cierre.md`. La carpeta `onboarding-cliente`
no existe como directorio ni contiene ninguna página; tampoco hay ningún enlace `[[wiki-link]]`
hacia ella desde ninguna otra página. El índice no la menciona. Para una agencia que ya tiene
un cliente activo ([[cliente-dental-sonrisa]]) el proceso de onboarding es conocimiento
procedural relevante que falta completamente.

**Diff propuesto**:
```
# Crear wiki/procedural/onboarding-cliente/sop-onboarding-cliente.md
# con frontmatter mínimo: type: sop, tier: procedural, decay_rate: medium, sources: []
# y stub de contenido a completar con INGEST del proceso real.
# O, si el SOP aún no existe, documentar en index.md como "pendiente".
```

---

## C4 — Asimetría en relaciones lead-cliente (trazabilidad incompleta)

| Campo | Valor |
|---|---|
| **clase** | contradicción entre páginas wiki |
| **severidad** | 4 |
| **páginas afectadas** | [[lead-dental-sonrisa]], [[cliente-dental-sonrisa]] |
| **alcance** | 2 |
| **impacto** | 42 |

**Evidencia**: `gen-lead-a-cliente` exige explícitamente que la página de cliente lleve la
relación `proviene_de` hacia el lead **y** que el historial de calls/objeciones del lead
se enlace desde el cliente. Revisando los frontmatters:

- `lead-dental-sonrisa.md` → `reemplaza_por: ["[[cliente-dental-sonrisa]]"]` ✓
- `cliente-dental-sonrisa.md` → `proviene_de: ["[[lead-dental-sonrisa]]"]` ✓

Hasta aquí correcto. Pero el gen exige además que "el historial de calls/objeciones del lead
se enlaza al cliente". `lead-dental-sonrisa.md` registra la objeción `usa: ["[[objecion-bot-malo]]"]`
y menciona la propuesta origen PROP-2026-019, pero **`cliente-dental-sonrisa.md` no tiene
ninguna relación que apunte a esos artefactos del lead** (no tiene `uses_historial`,
`calls_origen` ni similar, y tampoco un enlace textual explícito a la call del lead).
El cuerpo del cliente menciona `lead-dental-sonrisa` genéricamente, pero el gen pide
trazabilidad estructurada, no solo prosa. Esto es una contradicción parcial entre la regla
del gen y el estado de la página.

**Diff propuesto**:
```yaml
# cliente-dental-sonrisa.md — añadir en relations:
  historial_lead: ["[[lead-dental-sonrisa]]"]
  objeciones_lead: ["[[objecion-bot-malo]]"]
# Y en el cuerpo añadir sección "Historial del lead origen" con el resumen.
```

---

## C5 — Near-duplicado funcional: `n8n` y `Make` (redundancia)

| Campo | Valor |
|---|---|
| **clase** | redundancia (duplicado) |
| **severidad** | 2 |
| **páginas afectadas** | [[n8n]], [[make]] |
| **alcance** | 2 |
| **impacto** | 22 |

**Evidencia**: `make.md` dice "Cumple el mismo rol que [[n8n]]; la elección depende del
proyecto y las integraciones requeridas." `n8n.md` la llama "alternativa a [[Make]] según el
proyecto." Ambas tienen `type: entidad`, `tier: semantic`, `decay_rate: low`, `tags:
[herramienta, orquestacion, automatizacion]`. El contenido factual es casi idéntico en
estructura y longitud. No hay relación `reemplaza` entre ellas (correcto; son alternativas,
no sustituciones). Sin embargo, no existe una página de síntesis "Orquestadores" ni un campo
que agrupe estas entidades funcionalmente, lo que generará duplicación de contenido si se
agregan más detalles a cada una en el futuro. El gen `gen-consolidate` señalaría esto como
near-duplicado por rol funcional y tags identicos.

**Diff propuesto**:
```yaml
# Opción A (recomendada): añadir en ambas páginas un campo group o alias:
# n8n.md y make.md → frontmatter: group: "orquestadores"
# Opción B: crear wiki/semantic/herramientas/orquestadores.md como síntesis
# que agrupe ambas y cada una apunte a ella con depende_de o ver_tambien.
```

---

## Tabla resumen — ordenada por impacto descendente

| id | clase | sev | alcance | impacto |
|---|---|---|---|---|
| C1 | info vencida (precio) | 5 | 3 | **53** |
| C2 | info vencida (followup) | 5 | 2 | **52** |
| C4 | contradicción entre páginas wiki | 4 | 2 | **42** |
| C5 | redundancia (near-duplicado) | 2 | 2 | **22** |
| C3 | vacío (categoría sin cobertura) | 2 | 1 | **21** |

Top-3 candidatos por impacto: **C1, C2, C4**.

---

## Fricciones del gen (afinación)

### 1. Clase "info vencida en dominio de seguridad" — no encaja bien en ventas
El gen define la clase por analogía a seguridad informática (contraseñas, certificados, accesos).
En una agencia de ventas, los "dominios de riesgo alto" son los precios y los hitos comerciales,
no datos de seguridad. La rúbrica asigna `severidad: 5` a esta clase (la más alta junto a
"contradicción entre genes activos"), lo cual es correcto para precios vencidos, pero la
etiqueta literal "dominio de seguridad" es confusa para el auditor que trabaja en este sector.
**Señal de afinación**: la clase debería llamarse "info vencida en dominio de alto riesgo
operativo" o añadir sub-tipos por dominio, o bien la guía debería listar ejemplos de dominios
equivalentes (precios, SLAs, hitos de decisión comercial) para que el detector no quede
ambiguo al aplicarse fuera del dominio IT.

### 2. Asimetría de relaciones — sin clase propia
El candidato C4 (relación estructural exigida por un gen pero ausente en la página) no encaja
perfectamente en ninguna clase de la rúbrica. "Contradicción entre páginas wiki" (`sev: 4`)
es la más cercana, pero técnicamente no es una contradicción de contenido sino un incumplimiento
de un invariante estructural impuesto por el gen. Una clase "violación de invariante de gen"
(¿sev 4 o 5?) sería más precisa y evitaría forzar este defecto en la clase de contradicción.

### 3. Relaciones ad-hoc (`recibio_propuesta`, `tiene_followup`, `usada_en`) — fuera de esquema
Varias páginas usan verbos de relación no declarados en `relation_types` del manifiesto (el
manifiesto no tiene sección `relation_types`). El gen `gen-lint` detectaría esto como "campo
fuera de esquema", pero el gen `gen-auto-auditoria` no tiene clase de rúbrica para
"violación de esquema de relaciones". Actualmente estos verbos son lógicamente correctos y
aportan valor, pero no son detectables como defecto sin esta clase. **Señal**: o el manifiesto
debe declarar `relation_types` y el gen-lint lo valida, o la rúbrica de AUDIT añade
"campo/relación fuera de esquema declarado" (¿sev 1 o 2?).

### 4. `alcance` de C3 (categoría vacía) — ¿1 o 0?
La rúbrica define `alcance` como "nº de páginas/genes afectados". Para un vacío (categoría
sin ninguna página), el alcance literal es 0 páginas existentes, pero el impacto real es la
ausencia de cobertura para toda la categoría. Se usó alcance=1 contando la categoría como
unidad, pero la rúbrica no especifica cómo contar vacíos. Esto introduce ambigüedad en el
score de candidatos de tipo "vacío".
