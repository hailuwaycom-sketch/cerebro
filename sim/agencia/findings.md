# findings.md — EVOLVE (sandbox agencia / FlujoLab)

Reporte de fricciones del **genoma BASE** detectadas al correr el full loop
(onboard -> ingest -> query -> evolve) en el vertical de agencia de automatización, y propuestas
de mutacion. **Nada se aplico a `D:/cerebro/genome/`** (modo hibrido, [[gen-compuerta-mutacion]]).

---

## (a) Fricciones — donde el genoma BASE se quedo corto

### F1. El frontmatter base tiene un conjunto CERRADO de relaciones tipadas
[[gen-frontmatter-obligatorio]] fija `relations{usa, depende_de, contradice, reemplaza}`. El
vertical de agencia necesita relaciones de **proceso comercial** que no encajan en esas cuatro:
- conversion lead->cliente sin perdida de historial -> necesite inventar `reemplaza_por` y
  `proviene_de` (lo pide el propio seed-gen [[gen-lead-a-cliente]], pero el gen base no las
  contempla, asi que quedan "fuera del esquema").
- propuesta->lead, propuesta->precio, lead->follow-up -> invente `para_lead`, `recibio_propuesta`,
  `define_precio`, `tiene_followup`, `genero_caso`.
`reemplaza` ademas es ambiguo: en el gen base significa "fuente nueva sustituye a vieja", pero
una conversion lead->cliente NO es una sustitucion (el lead se conserva). Tuve que crear
`reemplaza_por` para no chocar semanticamente. Un agente menos cuidadoso usaria `reemplaza` y
borraria/huerfanizaria el lead — justo lo que el sector NO quiere.

### F2. [[gen-query]] no sabe comparar fechas: detecta decay pero no "vencimiento por fecha dura"
El gen base dice que QUERY advierte de "conocimiento vencido por `decay_rate`", pero `decay_rate`
es una *tendencia* (high/medium/low), no una fecha. Los precios de agencia caducan en una fecha
**exacta** (`valido_hasta: 2026-06-14`). El genoma base no define ningun campo de fecha-limite ni
le dice a QUERY que compare contra "hoy". Sin el seed-gen [[gen-precio-perecedero]] que tuve que
sembrar, el agente habria citado USD 2,400 como vigente el 2026-06-22 (8 dias vencido) con falsa
seguridad. Lo mismo con el follow-up vencido: el genoma base no tiene nocion de "item accionable
con fecha objetivo".

### F3. No hay lugar canonico para "items accionables con fecha" (follow-ups / tareas)
Los tiers base (working/episodic/semantic/procedural) son para *conocimiento*, no para *acciones
pendientes*. Un follow-up con `fecha_objetivo` es un hibrido: es working (decae) pero su valor esta
en su deadline, no en su contenido. Lo meti en `working/` con `estado: vencido` a mano, pero ningun
gen base gobierna su ciclo de vida (cuando se cierra, cuando escala, cuando alerta). LINT detecta
huerfanos y decay, pero no "deadline pasado sin cerrar".

---

## (b) Propuestas de mutacion al genoma BASE (concretas)

### M1. EDITAR [[gen-frontmatter-obligatorio]] — relaciones extensibles, no cerradas
De: `relations{usa, depende_de, contradice, reemplaza}` (lista fija).
A: mantener esas 4 como **nucleo reservado** y anadir explicitamente que el bloque `relations`
es **abierto/extensible**: los seed_genes del sector pueden declarar relaciones tipadas
adicionales (ej. `proviene_de`, `reemplaza_por`, `recibio_propuesta`), siempre que cada relacion
apunte con `[[wiki-link]]` y sea inversa-consistente (si A `proviene_de` B, B lleva la inversa).
Ademas, separar semanticamente `reemplaza` (sustitucion de fuente, la vieja se deprecia) de
`reemplaza_por`/`sucede_a` (cambio de estado con conservacion del historico). Esto evita que la
conversion lead->cliente se modele como un borrado.

### M2. NUEVO `gen-vigencia-temporal` — vencimiento por fecha dura, no solo por decay
Regla: "Una pagina puede declarar `valido_hasta: <fecha>` (o `fecha_objetivo`). [[gen-query]],
[[gen-lint]] y [[gen-consolidate]] DEBEN comparar ese campo contra la fecha actual: si ya paso,
la pagina se marca/trata como **vencida** y se advierte SIEMPRE antes de citarla, con
independencia de su `decay_rate`. `valido_hasta` es un vencimiento absoluto; `decay_rate` es la
pendiente de confianza. Ambos coexisten." Esto generaliza el seed-gen `gen-precio-perecedero` y
hace que CUALQUIER vertical con datos perecederos (precios, contratos, certificaciones, stock)
funcione sin sembrar un gen ad-hoc.

### M3. NUEVO `gen-accionables` (+ tier `tasks/` o `type: accionable`) — gobernar pendientes con deadline
Regla: "Los items accionables con fecha (follow-ups, tareas, renovaciones) se registran con
`type: accionable`, `fecha_objetivo` y `estado: {abierto|hecho|vencido}`. [[gen-lint]] los revisa:
si `fecha_objetivo` < hoy y `estado: abierto`, los reporta como **vencidos sin cerrar** y propone
escalar o cerrar. No se borran al vencer (quedan como historico de cuenta)." Cubre el hueco entre
'conocimiento' y 'tarea' que hoy ningun tier ni gen base atiende.

---

## Resumen para la compuerta
- 1 EDIT a gen base ([[gen-frontmatter-obligatorio]]).
- 2 genes NUEVOS (`gen-vigencia-temporal`, `gen-accionables`), ambos generalizaciones de
  fricciones que aparecieron sembrando genes ad-hoc en este vertical.
- Todas pasan por [[gen-compuerta-mutacion]]: propuesta -> aprobacion del fundador -> events.jsonl + commit.
