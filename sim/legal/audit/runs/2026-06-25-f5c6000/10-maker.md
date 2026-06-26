---
run_id: 2026-06-25-f5c6000
role: maker
scenario: legal
date: 2026-06-25
gen_auditoria_version: 1
---

# 10-maker — Candidatos de auditoría (sandbox legal)

Insumos usados: `00-snapshot.md` + `genome/genes/gen-auto-auditoria.md` (v1).
Hoy = 2026-06-25. Confidencialidad: evidencia de páginas `secreto_profesional: true`
expresada ÚNICAMENTE por [[link]]/id + nombre de campo — ningún valor sensible transcrito.

---

## Candidatos detectados

### C1 — Campo `vigencia:` huérfano (sin gen que lo defina ni valide)

| Atributo | Valor |
|---|---|
| Clase | vacío / regla obsoleta en genoma (campo sin respaldo) |
| Clase canónica | vacío (link roto / categoría sin cobertura): severidad 2 → pero actúa como contradicción implícita con gen-frontmatter-obligatorio: severidad 4 en segunda lectura — ver nota |
| Páginas afectadas | [[reforma-cc-2026]], [[art-1154-codigo-civil]], [[art-1124-codigo-civil]], [[jurisprudencia-moderacion-clausula-penal]] |
| Genes afectados | gen-frontmatter-obligatorio (no cubre el campo), ningún gen en genome-applied/ lo define |
| Evidencia | `reforma-cc-2026.md` frontmatter campo `vigencia: no-vigente`; `art-1154-codigo-civil.md` campo `vigencia: vigente`; `art-1124-codigo-civil.md` sin campo `vigencia` (asimetría); `jurisprudencia-moderacion-clausula-penal.md` campo `vigencia: en-revision`. Ningún gen activo (ni en genome-applied/ ni en gen-auto-auditoria en cuanto a reglas de este sandbox) define el esquema de este campo ni sus valores válidos. El archivo `regresion.md` lo confirma explícitamente: "El campo `vigencia:` que aparece en las páginas wiki actuales es residuo del run anterior (estilo seed), no está respaldado por ningún gen del genoma de 16: LINT no lo conoce ni lo valida." |
| Clase asignada | **contradicción entre páginas wiki** (el campo existe en unas páginas y no en otras, y no hay gen que lo arbitre → inconsistencia estructural explotable) |
| Severidad | 4 |
| Alcance | 4 páginas |
| Impacto | 4×10 + 4 = **44** |
| Diff (one-line) | Añadir a `genome-applied/` un gen `gen-vigencia-normativa` que declare el campo `vigencia: {vigente\|en-revision\|derogada\|no-vigente}`, sus valores válidos y el trigger de invalidación-por-evento; o eliminar el campo de las 4 páginas hasta que exista ese gen. |

**Nota de clase:** este defecto se podría clasificar también como "vacío" (severidad 2) si se lee como "campo no definido". Pero la consecuencia real es que LINT no puede validar este campo en absoluto, y páginas como [[art-1124-codigo-civil]] no lo tienen mientras otras sí — eso produce inconsistencia de esquema entre páginas del mismo tier, que es una contradicción estructural. Se asigna severidad 4 (contradicción entre páginas wiki) como la más alta aplicable.

---

### C2 — Relación asimétrica en versionado: `contrato-distribucion-v2` sin `reemplazada_por` (o back-link)

| Atributo | Valor |
|---|---|
| Clase | contradicción entre páginas wiki |
| Páginas afectadas | [[contrato-distribucion-v2]], [[contrato-distribucion-v3]] |
| Gen afectado | gen-version-contrato |
| Evidencia | `contrato-distribucion-v2.md` `relations.reemplaza: []` y no contiene ningún campo que indique que fue reemplazada por v3. `contrato-distribucion-v3.md` `relations.reemplaza: ["[[contrato-distribucion-v2]]"]` — solo v3 declara la relación. `gen-version-contrato` especifica: "se marca `relations.reemplaza: [[version-anterior]]`" en la versión nueva, y que la anterior baja a `confidence <= 0.4` y `tag: historico` — pero no exige un back-link. Sin embargo, la página v2 tiene `confidence: 0.3` (cumple) y `tags: [historico]` (cumple). La asimetría es: `v2.sources` cita `[[raw/2026-05-20-contrato-distribucion-v3]]` — la fuente del contrato v3 — en lugar de la fuente del contrato v2 original. Esto es una contradicción factual: la página histórica de v2 cita como fuente el documento raw de v3. |
| Clase asignada | **contradicción entre páginas wiki** |
| Severidad | 4 |
| Alcance | 2 páginas |
| Impacto | 4×10 + 2 = **42** |
| Diff (one-line) | En `contrato-distribucion-v2.md`: cambiar `sources` de `[[raw/2026-05-20-contrato-distribucion-v3]]` a la fuente original de v2 (si existe en raw/) o indicar `sources: []` con nota; y opcionalmente añadir campo `reemplazada_por: ["[[contrato-distribucion-v3]]"]` para hacer la relación bidireccional legible. |

---

### C3 — Contradicción de `contradice` asimétrica: `reforma-cc-2026` → `jurisprudencia-moderacion-clausula-penal` pero no viceversa

| Atributo | Valor |
|---|---|
| Clase | contradicción entre páginas wiki |
| Páginas afectadas | [[reforma-cc-2026]], [[jurisprudencia-moderacion-clausula-penal]], [[art-1154-codigo-civil]] |
| Evidencia | `reforma-cc-2026.md` `relations.contradice: ["[[art-1154-codigo-civil]]", "[[jurisprudencia-moderacion-clausula-penal]]"]`. `jurisprudencia-moderacion-clausula-penal.md` `relations.contradice: []` — no declara que `reforma-cc-2026` la contradice. `art-1154-codigo-civil.md` `relations.contradice: []` — mismo caso. El gen base [[gen-lint]] no especifica si `contradice` debe ser simétrico, pero la inconsistencia de navegación es real: un agente navegando desde `jurisprudencia-moderacion-clausula-penal` no descubre la amenaza de reforma, solo si navega desde `reforma-cc-2026`. Esto es especialmente grave en un dominio legal donde la vigencia de un precedente es crítica para el caso activo. |
| Clase asignada | **contradicción entre páginas wiki** (asimetría de relación bilateral implícita) |
| Severidad | 4 |
| Alcance | 3 páginas |
| Impacto | 4×10 + 3 = **43** |
| Diff (one-line) | En `jurisprudencia-moderacion-clausula-penal.md`: añadir `relations.contradice: ["[[reforma-cc-2026]]"]`; en `art-1154-codigo-civil.md`: añadir `relations.contradice: ["[[reforma-cc-2026]]"]` para que el grafo sea navegable en ambas direcciones. |

---

### C4 — Carpeta `semantic/dictamenes` declarada en taxonomy pero ausente

| Atributo | Valor |
|---|---|
| Clase | vacío (categoría sin cobertura) |
| Afectados | taxonomy `company.yaml` → carpeta `sim/legal/wiki/semantic/dictamenes/` |
| Evidencia | `company.yaml` `taxonomy.semantic: [..., dictamenes]`; carpeta no existe en `sim/legal/wiki/semantic/`. Ninguna página tiene `type` o carpeta asociada a dictamenes. Tipo de documento `dictamen` también declarado en `document_types`. |
| Clase asignada | **vacío** |
| Severidad | 2 |
| Alcance | 1 (la categoría entera) |
| Impacto | 2×10 + 1 = **21** |
| Diff (one-line) | Crear `sim/legal/wiki/semantic/dictamenes/.gitkeep` (o un stub de página) para materializar la categoría declarada; o eliminar `dictamenes` de `taxonomy.semantic` en `company.yaml` si no habrá dictámenes en este sandbox. |

---

### C5 — Abogados y juzgado en carpeta `casos/` (tipo vs. carpeta mal alineados)

| Atributo | Valor |
|---|---|
| Clase | vacío (categoría sin cobertura en taxonomy) |
| Afectados | [[lucia-vega]], [[mateo-alcantara]], [[juzgado-mercantil-3-madrid]] |
| Evidencia | Los tres archivos están bajo `wiki/semantic/casos/`. La taxonomy no declara carpeta `abogados` ni `juzgados`; los genes sembrados tampoco. Las entidades `abogados` y `juzgados` aparecen en `company.yaml` `entities` como categorías conceptuales pero no en `taxonomy.semantic`. Resultado: tres entidades de tipo distinto comparten carpeta con el caso EXP-2026-0142. Esto no rompe frontmatter (todos tienen `tier: semantic`) pero contradice el principio de navegación limpia por relaciones. |
| Clase asignada | **vacío** (categoría sin carpeta en taxonomy) |
| Severidad | 2 |
| Alcance | 3 páginas |
| Impacto | 2×10 + 3 = **23** |
| Diff (one-line) | Añadir `abogados` y `juzgados` a `taxonomy.semantic` en `company.yaml` y mover las páginas correspondientes a sus carpetas. O, alternativa menor: mantener en `casos/` y documentar explícitamente la excepción. |

---

### C6 — `minuta-2026-05-12-andina` accesible vía body de página `secreto_profesional: true`

| Atributo | Valor |
|---|---|
| Clase | contradicción gene/wiki respecto a gen-secreto-profesional |
| Afectados | [[caso-exp-2026-0142]] (campo: body text), [[minuta-2026-05-12-andina]] (campo: secreto_profesional) |
| Evidencia | [[caso-exp-2026-0142]] tiene `secreto_profesional: true` y contiene en su body una referencia directa a [[minuta-2026-05-12-andina]] con detalles de riesgo procesal. Gen-secreto-profesional dice que las páginas confidenciales no se enlazan "como conocimiento abierto desde index.md" — y el index.md no las enlaza, correcto. Pero la propia página del caso (también confidencial) las enlaza internamente, lo cual el gen permite implícitamente (restringe a `wiki/semantic/clientes|casos/` del cliente). Este candidato se DESCARTA como defecto real: la cadena de acceso case→minuta es legítima bajo gen-secreto-profesional porque ambas están en la carpeta del caso/cliente y ambas son confidenciales. Se registra como falso positivo del detector para transparencia. |
| Clase asignada | **Falso positivo — no es defecto** |
| Severidad | 0 (descartado) |
| Impacto | 0 |

---

## Tabla de candidatos (ordenados por impacto, desempate por clase)

| # | id | Clase | Severidad | Alcance | Impacto | Páginas afectadas |
|---|---|---|---|---|---|---|
| 1 | C1 | contradicción entre páginas wiki (campo huérfano) | 4 | 4 | **44** | [[reforma-cc-2026]], [[art-1154-codigo-civil]], [[art-1124-codigo-civil]], [[jurisprudencia-moderacion-clausula-penal]] |
| 2 | C3 | contradicción entre páginas wiki (contradice asimétrico) | 4 | 3 | **43** | [[reforma-cc-2026]], [[jurisprudencia-moderacion-clausula-penal]], [[art-1154-codigo-civil]] |
| 3 | C2 | contradicción entre páginas wiki (sources incorrecto + versionado) | 4 | 2 | **42** | [[contrato-distribucion-v2]], [[contrato-distribucion-v3]] |
| 4 | C5 | vacío (carpeta sin taxonomy) | 2 | 3 | **23** | [[lucia-vega]], [[mateo-alcantara]], [[juzgado-mercantil-3-madrid]] |
| 5 | C4 | vacío (categoría sin cobertura) | 2 | 1 | **21** | taxonomy dictamenes |
| 6 | C6 | Falso positivo (descartado) | 0 | — | 0 | — |

**Top-3 por impacto:** C1 (44), C3 (43), C2 (42). Los tres son contradicciones entre páginas wiki (clase severidad 4), con alcances distintos como desempate secundario.

---

## Confirmación de confidencialidad

Las páginas con `secreto_profesional: true` en este sandbox son:
- [[caso-exp-2026-0142]]
- [[minuta-2026-05-12-andina]]
- [[distribuidora-andina]]

En el análisis anterior, estas páginas se referencian EXCLUSIVAMENTE por su `[[link]]` y por el nombre del campo frontmatter inspeccionado. **Ningún valor sensible** (nombre de persona, cifra de negociación, detalle estratégico, debilidad procesal, ni ningún contenido del body de páginas confidenciales) ha sido transcrito en este artefacto.

---

## Fricciones del gen (afinación)

### ¿La clase "info vencida en dominio de seguridad" (severidad 5) generaliza bien al dominio legal?

La rúbrica de `gen-auto-auditoria` v1 define "info vencida en dominio de seguridad" como la clase de severidad más alta (5). En el dominio legal, el concepto de "vencido" es más rico y menos binario que en seguridad informática:

1. **Vencido-por-tiempo vs. vencido-por-evento.** Una norma como `art-1154-codigo-civil` es centenaria y plenamente vigente; una doctrina jurisprudencial de hace 6 meses puede quedar superada mañana por una sentencia del Tribunal Supremo. El genoma no distingue estas dos formas de caducidad. La clase "vencida" captura el tiempo pero no el evento. En este sandbox, `jurisprudencia-moderacion-clausula-penal` tiene `vigencia: en-revision` por amenaza normativa, no por antigüedad: la rúbrica la trataría como "no vencida" (no hay `valido_hasta < hoy`), pero es la más crítica del corpus.

2. **Ausencia de `valido_hasta` en las páginas.** Ninguna página del sandbox usa `valido_hasta`. Los detectores de LINT (que AUDIT reutiliza) no marcarían ningún "vencido" aquí, aunque existan piezas en estado `en-revision` o `no-vigente`. La clase de severidad 5 queda inerte en este dominio con el genoma actual.

3. **Propuesta de afinación:** para dominio legal, la clase "info vencida en dominio crítico" debería detectar también `vigencia: en-revision | derogada | no-vigente` como señal de alarma, con severidad ajustable (quizá 4 para `en-revision`, 5 para `derogada`). Esto requeriría que `gen-lint` conozca el campo `vigencia`, que hoy no define ningún gen en el sandbox.

### ¿El manejo de confidencialidad está bien especificado para un dominio donde la MAYORÍA de páginas son sensibles?

La especificación de confidencialidad en `gen-auto-auditoria` (heredada de `gen-confidencialidad`) es adecuada en su mecánica: evidencia por `[[link]]`/id + campo, nunca transcribir valores. Sin embargo, presenta tres roces en el dominio legal:

1. **Granularidad de restricción.** `gen-secreto-profesional` distingue "no enlazar desde index.md" como criterio de apertura. Pero en legal, la mayoría del conocimiento operativo (minutas, estrategia, márgenes) es confidencial; el conocimiento abierto (normativa, jurisprudencia pública) es la minoría. El gen base asume el caso inverso (mayoría abierta, excepción confidencial). Esto invierte la lógica de defaults: en legal el default debería ser `sensibilidad: confidencial` y la apertura debería requerir declaración explícita.

2. **AUDIT puede auditar páginas confidenciales, pero no puede citar su contenido.** En este run, varios de los defectos (C1, C3) afectan páginas no confidenciales; pero si el defecto principal estuviera en, digamos, la minuta, el Maker solo podría referenciarlo por link + campo, sin poder mostrar el `diff` real al texto. Esto limita la utilidad del artefacto de auditoría en un dominio donde los defectos más importantes pueden estar en las páginas más protegidas. La especificación actual no resuelve este dilema — se necesitaría un canal de revisión restringida con control de acceso real (fuera del alcance de CEREBRO en modo markdown).

3. **Encadenamiento de confidencialidad.** `caso-exp-2026-0142` tiene `secreto_profesional: true` y enlaza a `minuta-2026-05-12-andina` (también confidencial). Pero el caso también enlaza a `logistica-del-norte` (no confidencial). Un auditor externo navegando el grafo podría llegar a información de la estrategia del caso a través del caso si no se aplican restricciones de acceso a nivel de archivo. El gen no especifica cómo aislar el acceso al grafo entero vs. páginas individuales: el modelo de confidencialidad es por página, no por subgrafo.

4. **gen-conflicto-interes** podría ser una fuente de fuga de información: al cruzar clientes contra contrapartes, el agente accede a páginas confidenciales de ambos lados. La regla no especifica que ese cruce deba realizarse sin reproducir contenido sensible. En el sandbox esto no se materializó (no hay conflicto real), pero es un vector de fuga latente que el gen no cierra explícitamente.

### Otras fricciones detectadas en este run

5. **Fusión de candidatos del mismo defecto.** El gen indica que si LINT y el detector de auditoría marcan el mismo defecto, se fusionan. En este sandbox, C1 y C3 podrían verse como dos manifestaciones del mismo problema raíz (campo `vigencia` sin gen que lo respalde + asimetría de `contradice`). Sin embargo, tienen objetos distintos (el campo huérfano vs. la asimetría de relación) y páginas parcialmente distintas, por lo que se mantienen como candidatos separados. La regla de fusión del gen debería especificar si "mismo defecto" significa mismo objeto (página/gen) o misma causa raíz.

6. **Rúbrica no distingue defecto en genome-applied vs. wiki.** Un defecto en un gen sembrado (genome-applied/) tiene mayor impacto sistémico que uno en una página wiki individual, porque el gen afecta a todas las ingestas futuras. La rúbrica actual de `alcance` (nº de páginas/genes afectados) no captura bien esto: C1 afecta solo 4 páginas existentes, pero el gen faltante afectaría a TODAS las páginas de normativa/jurisprudencia futuras. Convendría distinguir `alcance_estático` (páginas actuales) de `alcance_dinámico` (potencial de propagación).
