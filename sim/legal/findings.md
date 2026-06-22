# findings — EVOLVE del sandbox legal (Vega & Alcántara)

Resultado de correr el full loop (onboard → ingest → query → evolve) sobre un
bufete. Reporta dónde el **genoma BASE** se quedó corto y propone mutaciones.
NO se aplica nada a `D:/cerebro/genome/` (modo híbrido, [[gen-compuerta-mutacion]]).

## (a) Fricciones con el genoma base

1. **Confidencialidad vs. apertura del conocimiento.**
   El genoma base asume que TODO conocimiento es abierto y enlazable desde
   `index.md`. En legal, la mayor parte (minutas, estrategia, márgenes) está bajo
   secreto profesional y NO debe tratarse como conocimiento abierto. `gen-ingest`
   y `gen-frontmatter-obligatorio` no contemplan un nivel de sensibilidad ni un
   campo `secreto_profesional`; hubo que inventarlo en un seed gene. Peor:
   `gen-query` cita "siempre las páginas-fuente y su confidence" — en legal eso
   filtraría datos protegidos. El genoma base no tiene un eje de confidencialidad.

2. **Relaciones tipadas insuficientes para citar precedentes.**
   El set fijo `relations{usa, depende_de, contradice, reemplaza}` no captura las
   relaciones jurídicas reales: una sentencia *se apoya en* / *cita* otra, un
   contrato *instancia* una cláusula-tipo, un dictamen *se fundamenta en* una
   norma. Tuve que aplastar "se apoya en" dentro de `usa`, perdiendo semántica que
   es justamente la que un abogado consulta. El genoma base no permite extender el
   vocabulario de relaciones por sector.

3. **Vigencia normativa != decay temporal.**
   El genoma modela obsolescencia solo por tiempo (`last_reinforced` + `decay_rate`
   en `gen-lint`/`gen-consolidate`). Pero una ley derogada o una jurisprudencia
   superada se invalida por un EVENTO (reforma, sentencia posterior), no por el
   paso del tiempo: el [[art-1154-codigo-civil]] es viejísimo y plenamente vigente,
   mientras que una doctrina de hace 1 mes puede quedar muerta mañana. No existe un
   campo `vigencia` ni una invalidación-por-evento; tuve que simularla con
   `contradice` desde [[reforma-cc-2026]], lo que es un parche.

## (b) Propuestas de mutación al genoma BASE (concretas)

1. **NUEVO gen-confidencialidad:** "Toda página lleva un campo `sensibilidad:
   {publico|interno|confidencial}` en el frontmatter (default `interno`). Las
   `confidencial` NO se anclan en `index.md` ni se citan textualmente en QUERY
   salvo autorización explícita; `gen-query` debe verificar `sensibilidad` antes de
   reproducir contenido o fuentes." (Generaliza el seed gen-secreto-profesional a
   cualquier sector con datos sensibles: salud, RRHH, M&A.)

2. **EDITAR gen-frontmatter-obligatorio:** de
   `relations{usa, depende_de, contradice, reemplaza}` (set cerrado)
   a `relations{usa, depende_de, contradice, reemplaza, + relaciones declaradas en
   company.yaml:relation_types}` (set extensible por manifiesto). Permite que cada
   sector añada relaciones tipadas (`cita`, `se_apoya_en`, `instancia`,
   `fundamenta`) sin romper las base.

3. **NUEVO gen-vigencia (o EDITAR gen-lint):** "Distingue obsolescencia por TIEMPO
   (decay) de invalidación por EVENTO. Añade campo `vigencia:
   {vigente|en-revision|derogada|no-vigente}`. Cuando una página marca `reemplaza`
   o `contradice` a otra por cambio normativo/jurisprudencial, `gen-lint` baja la
   página afectada a `vigencia: en-revision` y lo advierte en QUERY, con
   independencia de su antigüedad o `decay_rate`."

## Notas de ejecución
- Idempotencia OK: las versiones de contrato usan `reemplaza` sin borrar la vieja.
- `gen-conflicto-interes` (seed) se disparó sin conflicto real (Logística del
  Norte no es cliente) — quedó documentado como chequeo pasado, no como alerta.
- El seed `gen-secreto-profesional` cubre el síntoma; la propuesta (b.1) lo eleva
  al genoma base como eje transversal, que es donde corresponde.
