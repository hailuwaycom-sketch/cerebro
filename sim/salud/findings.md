# findings.md — EVOLVE del sandbox SALUD (Clínica Vida Serena)

Salida del paso EVOLVE tras correr el full loop (onboard → ingest → query). Reporta dónde el
**genoma BASE** (los 10 genes de `D:/cerebro/genome/genes/`) se quedó corto frente al sector
salud, y propone mutaciones CONCRETAS. **Nada se aplica a `D:/cerebro/genome/`** — modo
propuesta, pasa por [[gen-compuerta-mutacion]].

---

## (a) Fricciones — dónde el genoma base se quedó corto

1. **Privacidad / PII no existe en el genoma base.** `gen-frontmatter-obligatorio` enumera un
   set fijo de campos (`title, type, tier, tags, confidence, created, last_reinforced,
   decay_rate, sources, relations`) que **no incluye `confidential` ni `pii`**. Nada en los
   genes base impide que `gen-consolidate` **promueva** una página de paciente de tier o la
   funda con conocimiento abierto, ni que `gen-ingest` ingrese PII real. En salud esto es un
   riesgo legal/ético de primer orden; tuve que inventar el campo a nivel de seed-gen.

2. **La caducidad es solo "confianza que baja", no "peligro que alerta".** `gen-lint` detecta
   páginas "vencidas según `last_reinforced + decay_rate`" y `gen-query` advierte "conocimiento
   vencido", pero el genoma base trata el vencimiento como degradación blanda de `confidence`.
   No existe el concepto de **`valid_until` duro** ni de **status `vencido` como hallazgo de
   seguridad prioritario**. Un protocolo clínico caducado no es "menos confiable": es accionable
   como incidente (lo demostró [[INC-2026-014]]). El genoma base no distingue decaimiento
   estadístico de caducidad normativa.

3. **Relaciones tipadas cerradas a un set genérico.** `gen-frontmatter-obligatorio` fija
   `relations{usa, depende_de, contradice, reemplaza}`. El dominio clínico necesita aristas
   como `presenta_patologia`, `tratado_con`, `segun_protocolo`, `indica`, `tratada_segun`,
   `afecta_a`, `reportado_por`. Las usé igual, pero quedan **fuera del contrato del gen base**,
   así que `gen-lint` no puede validarlas (no sabe que una ficha de fármaco huérfana es un
   error) y otro agente podría tratarlas como typos. El set de relaciones debería ser
   **extensible por el manifiesto**, no hardcodeado.

---

## (b) Propuestas de mutación al genoma BASE (concretas, NO aplicadas)

1. **NUEVO `gen-confidencialidad`** (fundamental, transversal a sectores sensibles):
   "Toda página marcada `confidential: true` lleva además `pii: <anonimizado|seudonimizado|none>`,
   NO se promueve de tier ni se fusiona por `gen-consolidate`, y `gen-query` devuelve solo su
   resumen sin exponer campos PII. Si `gen-ingest` detecta PII real en la fuente, se detiene y
   exige anonimización antes de crear la página." `target_tier: n/a (regla transversal)`.

2. **EDITAR `gen-frontmatter-obligatorio`** (v1 → v2): de
   *"frontmatter válido: `title, type, tier, tags, confidence, created, last_reinforced,
   decay_rate, sources, relations{usa, depende_de, contradice, reemplaza}`"*
   a
   *"…campos base + campos opcionales declarados por el manifiesto (`confidential`, `pii`,
   `valid_until`, `status`); y `relations` con un **set base extensible**: además de
   `{usa, depende_de, contradice, reemplaza}`, el `company.yaml` puede declarar relaciones
   tipadas propias del sector, que `gen-lint` tratará como válidas."* — resuelve fricciones 1 y 3.

3. **EDITAR `gen-lint`** (v1 → v2): de
   *"(c) páginas vencidas según `last_reinforced` + `decay_rate`"*
   a
   *"(c) páginas vencidas por `last_reinforced + decay_rate` **o por `valid_until` < hoy**; si la
   página es un protocolo/SOP con `valid_until` vencido y **sin** una página que la `reemplaza`,
   se marca `status: vencido` y se eleva como **hallazgo de seguridad prioritario** (no como
   simple baja de confidence)."* — formaliza la caducidad dura de la fricción 2.

   *(Alternativa equivalente: **NUEVO `gen-caducidad-dura`** que encapsule `valid_until` +
   status `vencido` + advertencia obligatoria en QUERY, dejando `gen-lint` sin tocar.)*

---

## Nota de reproducibilidad
Mismo `sim/salud/company.yaml` → mismos 3 seed-genes en `genome-applied/`. Las propuestas de
arriba son señales para el cerebro real; ninguna se escribió en `D:/cerebro/genome/`.
