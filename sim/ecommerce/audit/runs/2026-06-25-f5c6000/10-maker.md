---
run_id: 2026-06-25-f5c6000
role: maker
gen_version: 1
audit_date: 2026-06-25
candidates: 6
---

# Maker — Candidatos de auditoría (ecommerce 2026-06-25-f5c6000)

Método: esqueleto completo (frontmatter + grafo de relaciones de todas las páginas) → drill-down
en páginas flagged. Los detectores usados son los definidos en [[gen-auto-auditoria]]:
LINT (huérfanos, vencidos, verbos fuera de esquema), CONSOLIDATE (near-duplicados) y el detector
nuevo de redundancia/obsolescencia de genoma.

---

## C1 — Vacío: `[[cat-ollas-y-sartenes]]` y `[[cat-organizadores]]` son links rotos

| campo | valor |
|---|---|
| clase | vacío (link roto / categoría sin cobertura) |
| severidad | 2 |
| páginas afectadas | [[SKU-OLL-0900]], [[SKU-ORG-1450]], taxonomía `company.yaml` |
| alcance | 3 (2 páginas SKU + declaración taxonomía) |
| impacto | 2×10 + 3 = **23** |

**Evidencia.** `SKU-OLL-0900.md` declara `relations.usa: ["[[cat-ollas-y-sartenes]]"]`;
`SKU-ORG-1450.md` declara `relations.usa: ["[[cat-organizadores]]"]`. Ninguna de las dos
páginas de categoría existe bajo `wiki/semantic/categorias/`. La taxonomía del manifiesto
lista `categorias: ["cafeteras", "ollas-y-sartenes", "organizadores"]` como entidades del sector;
el ONBOARD las marcó como categorías declaradas, pero su siembra no se completó — solo
`cat-cafeteras.md` fue creada. El nodo `[[index.md]]` no enlaza estas dos categorías (sección
`## Memoria / semantic / categorias` lista solo `[[cat-cafeteras]]`), confirmando el vacío.

**diff sugerido.**
```
Crear:
  wiki/semantic/categorias/cat-ollas-y-sartenes.md
  wiki/semantic/categorias/cat-organizadores.md
Actualizar wiki/index.md → sección "categorias" añadir [[cat-ollas-y-sartenes]], [[cat-organizadores]].
```

---

## C2 — Vacío: `TKT-5550` referenciado en [[SKU-OLL-0900]] pero la página no existe

| campo | valor |
|---|---|
| clase | vacío (link roto) |
| severidad | 2 |
| páginas afectadas | [[SKU-OLL-0900]] |
| alcance | 1 |
| impacto | 2×10 + 1 = **21** |

**Evidencia.** `SKU-OLL-0900.md` frontmatter: `referido_por: ["[[TKT-5550]]"]`.
No existe `wiki/semantic/tickets/TKT-5550.md`. El body de la página menciona
"[[TKT-5550]] preguntó por disponibilidad; se respondió con la fecha de reposición",
lo que indica que el ticket fue procesado en `raw/tickets-soporte-2026-06` pero la
página destino nunca se creó. El [[index.md]] tampoco lista TKT-5550 en la sección `tickets`.

**diff sugerido.**
```
Crear wiki/semantic/tickets/TKT-5550.md con frontmatter type: entidad, tier: semantic,
  decay_rate: high, sobre_sku: [[SKU-OLL-0900]], motivo: consulta-disponibilidad.
Actualizar wiki/index.md → tickets añadir [[TKT-5550]].
```

---

## C3 — Vacío: clientes `cli-andres-gil` y `cli-jorge-lemus` mencionados pero sin páginas

| campo | valor |
|---|---|
| clase | vacío (link roto / entidades sin cobertura) |
| severidad | 2 |
| páginas afectadas | [[TKT-5530]], [[TKT-5561]] |
| alcance | 2 |
| impacto | 2×10 + 2 = **22** |

**Evidencia.** `TKT-5530.md` body: "cliente Andrés Gil (cli-andres-gil)" — el campo
`abierto_por: []` está vacío y no existe `wiki/semantic/clientes/cli-andres-gil.md`.
`TKT-5561.md` body: "cliente Jorge Lemus (cli-jorge-lemus)" — mismo patrón:
`abierto_por: []` vacío y no existe `wiki/semantic/clientes/cli-jorge-lemus.md`.
En [[SKU-CAF-2200]] `referido_por` lista TKT-5530 y TKT-5561 pero el grafo de
clientes está incompleto: [[cli-marcela-ortiz]] tiene páginas, los otros dos no.
Detectado por LINT (relación `abierto_por` vacía cuando el body menciona la entidad
cliente, y entidades cliente mencionadas sin página).

**diff sugerido.**
```
Crear wiki/semantic/clientes/cli-andres-gil.md
Crear wiki/semantic/clientes/cli-jorge-lemus.md
Actualizar TKT-5530.md → abierto_por: ["[[cli-andres-gil]]"]
Actualizar TKT-5561.md → abierto_por: ["[[cli-jorge-lemus]]"]
Actualizar wiki/index.md → clientes añadir [[cli-andres-gil]], [[cli-jorge-lemus]]
```

---

## C4 — Vacío: `procedural/sops-soporte` declarado en taxonomía sin ninguna página

| campo | valor |
|---|---|
| clase | vacío (categoría declarada sin cobertura) |
| severidad | 2 |
| páginas afectadas | taxonomía `company.yaml` |
| alcance | 1 |
| impacto | 2×10 + 1 = **21** |

**Evidencia.** `company.yaml` taxonomy: `procedural: [sops-soporte, sops-devoluciones]`.
Solo existe `wiki/procedural/sop-devoluciones.md` (nota: el archivo no está en una subcarpeta
`sops-devoluciones/`, está directamente en `procedural/` — inconsistencia de estructura menor,
pero el punto principal es que no existe ningún SOP de soporte). Dado que hay 4 tickets de
soporte activos y una síntesis con alerta de proveedor, la ausencia de un SOP de soporte es
un vacío operativo real. Detectado por LINT (categoría declarada = cero páginas).

**diff sugerido.**
```
Crear wiki/procedural/sop-soporte.md (al menos con frontmatter + esqueleto de SOP de
  atención de tickets, referenciando [[sop-devoluciones]] para la rama de RMA).
```

---

## C5 — Contradicción wiki: `company.yaml` entities.productos omite SKU-CAF-2201 y SKU-ORG-1450

| campo | valor |
|---|---|
| clase | contradicción entre páginas wiki |
| severidad | 4 |
| páginas afectadas | [[SKU-CAF-2201]], [[SKU-ORG-1450]], `company.yaml` entities.productos |
| alcance | 3 |
| impacto | 4×10 + 3 = **43** |

**Evidencia.** `company.yaml` declara:
```yaml
entities:
  productos: ["SKU-CAF-2200", "SKU-OLL-0900"]
```
Sin embargo el wiki tiene 4 páginas de producto: `SKU-CAF-2200`, `SKU-CAF-2201`,
`SKU-OLL-0900`, `SKU-ORG-1450`. El [[index.md]] lista los 4 (`[[SKU-CAF-2201]]`,
`[[SKU-ORG-1450]]`). `SKU-CAF-2201.md` y `SKU-ORG-1450.md` tienen frontmatter completo,
sources apuntan a `raw/catalogo-2026-06`, y existen relaciones entrantes desde otros nodos
(`cat-cafeteras.md` lista ambas variantes; `prov-menajeglobal.md` suministra `SKU-ORG-1450`).
Esto es una contradicción directa entre la fuente de verdad del manifiesto (que define las
entidades del sistema) y el estado real de la wiki: el manifiesto es incorrecto respecto al
estado vigente del corpus.

Nota: `regresion.md` no aborda esta discrepancia (se enfoca en `relation_types` y
`source_trust`), por lo que no es defecto ya documentado.

**diff sugerido.**
```yaml
# company.yaml → entities.productos:
productos: ["SKU-CAF-2200", "SKU-CAF-2201", "SKU-OLL-0900", "SKU-ORG-1450"]
```

---

## C6 — Contradicción entre gen-dato-volatil (aplicado) y los datos vencidos sin `valido_hasta` en frontmatter

| campo | valor |
|---|---|
| clase | contradicción entre páginas wiki |
| severidad | 4 |
| páginas afectadas | [[SKU-CAF-2200]], [[SKU-CAF-2201]], [[SKU-OLL-0900]], [[SKU-ORG-1450]] |
| alcance | 4 |
| impacto | 4×10 + 4 = **44** |

**Evidencia.** `gen-dato-volatil` (genome-applied) prescribe:
> "se marcan con su propio `decay_rate: high` y un campo `valido_a: <fecha>`" y "al consultar
> (QUERY), si `valido_a` es anterior a hoy, el agente lo ADVIERTE".

Hoy es 2026-06-25. Todos los campos volátiles en las 4 fichas de SKU tienen
`valido_a: 2026-06-20` (5 días vencidos). Las fichas aplican correctamente la estructura de
tabla con `valido_a` en el body. Sin embargo:

1. El `valido_a` está en el **cuerpo** (tabla markdown), no en el **frontmatter**, lo que
   impide que LINT y QUERY lo detecten automáticamente como campo estructurado sin parsear
   prosa. `gen-dato-volatil` dice "campo `valido_a`" pero no especifica si es frontmatter o
   body — ambigüedad en el gen que produce una contradicción práctica entre la intención del
   gen (detección automática) y la implementación (campo enterrado en prosa).
2. El `regresion.md` reconoce esto como la fricción no resuelta (M4 propone
   `volatile_fields` en frontmatter) pero la ficción **ya existe aplicada** en las páginas
   actuales — la implementación observada contradice lo que el gen nuevo (M4, en el genoma
   base actualizado según `regresion.md`) prescribiría.

Esto es una contradicción real entre el estado del wiki (campos `valido_a` en body) y la
prescripción de M4 del genoma en evolución (campos en frontmatter estructurado). Dado que
los datos están vencidos desde hace 5 días y QUERY no puede detectarlo sin parsear prosa, el
defecto tiene impacto operativo inmediato.

**diff sugerido.**
```
Añadir en frontmatter de cada ficha SKU:
  volatile_fields:
    - { campo: precio, valido_a: 2026-06-20 }
    - { campo: stock,  valido_a: 2026-06-20 }
    - { campo: estado, valido_a: 2026-06-20 }
(Mantener tabla en body como display; el frontmatter es lo estructurado y detectable.)
Actualizar gen-dato-volatil (aplicado) para aclarar que valido_a va en frontmatter
  como volatile_fields, no solo en cuerpo.
```

---

## Tabla resumen — candidatos ordenados por impacto desc

| ID | clase | páginas/genes afectados | sev | alcance | impacto |
|---|---|---|---|---|---|
| C6 | contradicción entre páginas wiki | [[SKU-CAF-2200]], [[SKU-CAF-2201]], [[SKU-OLL-0900]], [[SKU-ORG-1450]] + gen-dato-volatil | 4 | 4 | **44** |
| C5 | contradicción entre páginas wiki | [[SKU-CAF-2201]], [[SKU-ORG-1450]], company.yaml | 4 | 3 | **43** |
| C1 | vacío | [[SKU-OLL-0900]], [[SKU-ORG-1450]], company.yaml | 2 | 3 | **23** |
| C3 | vacío | [[TKT-5530]], [[TKT-5561]] + clientes faltantes | 2 | 2 | **22** |
| C2 | vacío | [[SKU-OLL-0900]] / [[TKT-5550]] faltante | 2 | 1 | **21** |
| C4 | vacío | procedural/sops-soporte (taxonomía) | 2 | 1 | **21** |

Desempate C2 vs C4: misma clase, mismo impacto → orden por ruta alfabética:
`company.yaml` (C4) < `wiki/semantic/productos/SKU-OLL-0900.md` (C2) → **C4 precede a C2**.

Top-3 por impacto: **C6 (44), C5 (43), C1 (23)**.

---

## Fricciones del gen (afinación)

### F1 — `valido_a` en body vs frontmatter: ambigüedad en el descriptor del gen

`gen-dato-volatil` dice "campo `valido_a`" sin precisar si es frontmatter o cuerpo. En la
práctica el sandbox lo implementó en una tabla markdown del body (más legible para humanos),
pero LINT/QUERY no pueden parsearlo automáticamente. La rúbrica clasifica esto como
"contradicción entre páginas wiki" (severidad 4) porque la intención del gen (detección
automática) se contradice con la implementación observada — pero podría también clasificarse
como "regla obsoleta/deprecable" (severidad 3) si se considera que el gen simplemente está
incompleto, no que haya contradicción. La distinción afecta el score en 10 puntos.

**Recomendación de afinación:** el gen debería especificar explícitamente el tier del campo
(`frontmatter.volatile_fields` vs body-display). Sin esta precisión, el detector no sabe si
está frente a una contradicción (implementación incorrecta) o a un vacío de especificación
(gen incompleto). Sugiero añadir una cuarta clase: "especificación incompleta de gen"
(severidad 3.5 o entre regla obsoleta y contradicción).

### F2 — Redundancia vs síntesis-de-volumen: ambigüedad para el detector de duplicados

`gen-sintesis-tickets` produce una sola página `sintesis-portafiltro-aurora.md` que agrega 4
tickets. El detector CONSOLIDATE busca "duplicados / near-duplicados". Los 4 tickets son
near-iguales en motivo (portafiltro) pero son entidades distintas (distintos clientes,
fechas, disposiciones). Para CONSOLIDATE son near-duplicados elegibles para fusión; para
`gen-sintesis-tickets` son eventos que YA fueron correctamente reducidos a una síntesis.

La rúbrica no distingue "near-duplicados que deben fusionarse" de "eventos de alto volumen
que ya fueron sintetizados correctamente". En este dominio de alto volumen (reseñas, tickets),
CONSOLIDATE podría disparar falsos positivos sobre entidades episódicas que el gen de síntesis
ya procesó bien. El auditor debería verificar explícitamente si existe una síntesis antes de
marcar tickets near-idénticos como redundancia.

**Recomendación de afinación:** añadir al detector de CONSOLIDATE una comprobación:
"si todos los near-duplicados tienen `agregado_en` apuntando a una misma síntesis existente,
NO son candidatos a redundancia — la síntesis es el artefacto correcto". Sin esta regla, el
mismo conjunto de tickets puede generar candidatos en dos clases distintas (redundancia y
síntesis correcta) lo que viola el principio de un-solo-candidato-por-defecto.

### F3 — `alcance` basado en páginas vs instancias de campo

La rúbrica define `alcance = nº de páginas/genes afectados`. Para C6 (datos volátiles
vencidos), el impacto real no es solo en 4 páginas: son 12 campos vencidos (3 por SKU x 4
SKUs). El alcance-página subestima el riesgo operativo de un dominio con datos de alta
rotación (precio cambia diariamente; stock cambia por hora). Un sector e-commerce/retail
justificaría un alcance ponderado por frecuencia de consulta o por "campos que vencen",
no solo por número de páginas. La rúbrica actual es correcta para dominios de baja volatilidad
pero produce scores bajos para defectos de alta densidad de campos volátiles.
