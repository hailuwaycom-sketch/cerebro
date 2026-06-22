# findings.md — EVOLVE (sandbox PRODUCCIÓN / manufactura)

Resultado de correr el full loop sobre **Forjados del Valle S.A.** Las propuestas son al
**genoma BASE** (`D:/cerebro/genome/`). NO se aplicó nada: pasan por [[gen-compuerta-mutacion]].

---

## (a) Fricciones — dónde el genoma BASE se quedó corto

### F1. `relations` tipadas son un conjunto cerrado e insuficiente para trazabilidad
[[gen-frontmatter-obligatorio]] fija `relations{usa, depende_de, contradice, reemplaza}`.
La manufactura es intrínsecamente un **grafo de trazabilidad direccional**: lote
`producido_en` máquina, `bajo_orden` OP, `de_producto` SKU, NCR `afecta_a` lote, mtto
`mantiene_a` máquina. Forzar todo eso a `usa`/`depende_de` borra la semántica que hace útil
la consulta ("qué lotes pasaron por PR-200" se vuelve indistinguible de "qué usa PR-200").
Tuvimos que inventar verbos fuera de esquema (`producido_en`, `afecta_a`, `mantiene_a`),
que el genoma base no reconoce ni un futuro LINT validaría.

### F2. No hay distinción entre conocimiento ESTABLE y EVENTO FECHADO
El genoma trata `decay_rate` como un campo libre, pero no hay regla que separe **entidades
estables** (máquina, producto, SOP -> decay bajo) de **eventos fechados** (NCR, OP, hoja de
mantenimiento -> decay alto). En manufactura esto es crítico: una NCR de hace 8 meses no
debe pesar igual que la ficha técnica vigente. CONSOLIDATE/LINT no tienen criterio para
decaer distinto un evento que un hecho; lo resolvimos a mano por convención de tags.

### F3. Entidades con ESTADO MUTABLE chocan con la idempotencia "no dupliques"
[[gen-ingest]] dice "si la página existe, actualiza y refuerza". Pero una máquina tiene un
campo `estado` (operativa -> en-mantenimiento -> operativa) que **cambia con cada evento**, y
a la vez un **historial** que debe preservarse. El genoma base no distingue "actualizar un
campo de estado in-place" de "registrar un evento histórico nuevo". Sin el gen de sector,
INGEST tendería a sobrescribir el estado perdiendo historial, o a duplicar la página.

---

## (b) Propuestas de mutación al genoma BASE (concretas, vía compuerta)

### M1. EDITAR [[gen-frontmatter-obligatorio]] — relaciones extensibles con namespace
De: `relations{usa, depende_de, contradice, reemplaza}` (conjunto cerrado).
A: `relations` con un **núcleo reservado** `{usa, depende_de, contradice, reemplaza}` MÁS
relaciones de dominio declaradas por el genoma de sector (ej. `seed_genes` puede registrar
verbos como `producido_en`, `afecta_a`, `mantiene_a`). LINT valida contra
núcleo ∪ verbos-de-sector; cualquier otro verbo es huérfano semántico. Sube `version` a 2.

### M2. NUEVO `gen-clase-temporal` — clasificar página como `estable` | `evento`
Regla exacta: "Toda página de `wiki/` declara `clase: estable | evento`. `evento` (NCR,
orden, registro fechado, intervención) nace con `decay_rate: high` y `fecha_evento`
obligatoria; su `confidence` NO se refuerza por reingesta del mismo evento. `estable`
(entidad, concepto, SOP) usa `decay_rate: low|medium`. CONSOLIDATE y LINT decaen y promueven
según `clase`, no solo `decay_rate`." target_tier: aplica a todo el sistema (gen fundamental).

### M3. NUEVO `gen-entidad-con-estado` — estado in-place + historial enlazado
Regla exacta: "Una entidad con ciclo de vida (campo `estado` en frontmatter) se ACTUALIZA
in-place: INGEST cambia `estado` + `last_reinforced` en la misma página, nunca la duplica.
Cada cambio de estado DEBE originarse en una página `clase: evento` que enlace
`<verbo>_a` la entidad (ej. `mantiene_a`), preservando el historial. LINT marca como
inconsistente toda entidad cuyo `estado` cambió sin un evento que lo respalde."
target_tier: fundamental (refuerza la idempotencia de [[gen-ingest]]).

---

## Nota de reproducibilidad
Mismo `company.yaml` -> mismos 3 genes sembrados -> mismas 13 páginas. Las 3 propuestas
generalizan los genes de sector a reglas base reutilizables por otros verticales
(salud: episodio clínico = evento; legal: caso = entidad con estado; ecommerce: pedido =
evento, SKU = estable).
