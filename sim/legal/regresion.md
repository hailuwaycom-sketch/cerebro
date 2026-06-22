# regresión — genoma NUEVO (16 genes) vs. fricciones del bufete

Verificación de si las 3 fricciones reportadas en `findings.md` quedan resueltas
por las mutaciones aplicadas al genoma base. Re-derivo el frontmatter de un caso
real por fricción aplicando el gen nuevo. Solo lectura del genoma; no lo modifico.

Fecha de regresión: 2026-06-22 · Sandbox: Vega & Alcántara Abogados.

---

## Fricción 1 — Confidencialidad vs. apertura  →  M3 gen-confidencialidad

**Caso real:** la minuta de estrategia `raw/2026-05-12-minuta-distribuidora-andina.md`
(margen de negociación 50.000 EUR, debilidad procesal del cliente).

**Re-derivación con el gen nuevo** (sustituye al parche seed `secreto_profesional: true`):

```yaml
title: "Minuta 2026-05-12 — Distribuidora Andina"
type: fuente
tier: semantic
clase: evento                 # gen-clase-temporal: es una call fechada
fecha_evento: 2026-05-12
sensibilidad: confidencial    # M3 — eje transversal, reemplaza al campo seed
tags: [minuta, estrategia]
confidence: 0.7               # gen-confianza-por-fuente: fuente interna
decay_rate: high
relations:
  depende_de: ["[[caso-exp-2026-0142]]", "[[distribuidora-andina]]"]
```

**Efecto del gen:** `sensibilidad: confidencial` activa las 3 reglas de M3:
(1) NO se ancla en index.md; (2) no se promueve ni se fusiona por CONSOLIDATE;
(3) QUERY no cita su contenido ni la fuente — responde con referencia indirecta.
Además M3 manda que INGEST **se detenga y pregunte** ante PII real sin anonimizar
(aquí: rep. legal Sr. Ortega + cifra sensible).

**VEREDICTO: ✅ resuelto.** El eje de sensibilidad sube del seed al genoma base y
es transversal (sirve a salud/RRHH/M&A, no solo legal). Resuelve además el choque
con `gen-query` ("cita siempre las fuentes"): M3 es la excepción explícita que
gen-query no contradice porque la confidencialidad prevalece.
*Matiz:* `gen-query` (v1) aún dice literalmente "citando **siempre** las páginas-fuente"
y no se editó para remitir a M3; la resolución es por precedencia de gen, no por
texto cruzado. Funciona, pero el "siempre" es redactura pendiente (no bloqueante).

---

## Fricción 2 — Relaciones tipadas para citar precedentes  →  M1 frontmatter v3 + relation_types

**Caso real:** `jurisprudencia-moderacion-clausula-penal.md` — la SAP Madrid 540/2025
**se apoya en** la STS 1023/2024; ambas **citan** el art. 1154 CC. Antes hubo que
aplastar "se apoya en" dentro de `usa`, perdiendo la semántica que el abogado consulta.

**Requisito de manifiesto:** declarar los verbos jurídicos en
`sim/legal/company.yaml: relation_types`. El manifiesto del sandbox legal **todavía
no los declara** (el `company.example.yaml` ya trae el patrón con `relation_types:
[recibio_propuesta, proviene_de, define_precio]`). Para legal habría que añadir:

```yaml
# sim/legal/company.yaml
relation_types: [cita, se_apoya_en, fundamenta, instancia]
```

**Re-derivación con el gen nuevo:**

```yaml
relations:
  cita: ["[[art-1154-codigo-civil]]", "[[art-1124-codigo-civil]]"]   # antes: usa
  se_apoya_en: ["[[sts-1023-2024]]"]   # SAP→STS; antes aplastado en usa
  contradice: []
  reemplaza: []
```

**Efecto del gen:** M1 abre `relations` (núcleo `{usa,depende_de,contradice,reemplaza}`
∪ declarados). LINT valida cada verbo contra esa unión y marca los no declarados.
La semántica jurídica deja de perderse.

**VEREDICTO: ✅ resuelto (acción pendiente de manifiesto).** El mecanismo existe y
es correcto; falta el paso de configuración (declarar los 4 verbos en el
company.yaml del bufete) y re-derivar la página, que hoy sigue con `usa`. Es
configuración, no defecto del genoma. Sin esa declaración LINT marcaría `cita`/
`se_apoya_en` como verbos no válidos: la resolución depende de completar M1 en el manifiesto.

---

## Fricción 3 — Vigencia normativa ≠ decay temporal  →  M2 gen-vigencia-temporal

**Caso real:** `art-1154-codigo-civil` (antiquísimo y plenamente vigente) vs. la
doctrina reciente que la reforma `reforma-cc-2026` puede invalidar por EVENTO.

**Re-derivación con el gen nuevo:**

```yaml
# art-1154-codigo-civil
valido_hasta: null            # M2: no caduca por fecha; vigente sine die
# si la reforma entrara en vigor el 2027-01-01:
#   reforma-cc-2026 -> reemplaza -> art-1154   (invalidación por evento/relación)

# jurisprudencia-moderacion-clausula-penal
# riesgo de quedar superada por reforma-cc-2026 (contradice), no por tiempo
```

**Lo que M2 SÍ resuelve:** desacopla la caducidad **dura por fecha** (`valido_hasta`)
del decay **blando** (`decay_rate`). Una página vencida por fecha la advierte QUERY
SIEMPRE, con independencia de `confidence`/`decay_rate`, y LINT la eleva como
hallazgo prioritario en dominios de seguridad (legal incluido). El art. 1154 ya
NO se degradaría por antigüedad: correcto.

**Lo que M2 NO resuelve:** la invalidación por **EVENTO** (ley derogada, sentencia
superada). M2 es fecha-céntrico (`valido_hasta: YYYY-MM-DD`); una derogación no tiene
fecha conocida de antemano. El genoma nuevo NO añade el campo enum `vigencia:
{vigente|en-revision|derogada|no-vigente}` que pedía la propuesta b.3 de findings.
La invalidación por evento sigue modelándose con `reemplaza`/`contradice` desde
`reforma-cc-2026` — **el mismo parche** que findings denunciaba. El campo `vigencia`
que aparece en las páginas wiki actuales es residuo del run anterior (estilo seed),
no está respaldado por ningún gen del genoma de 16: LINT no lo conoce ni lo valida.

**VEREDICTO: 🟡 parcial.** Resuelve decay-por-tiempo vs. caducidad-por-fecha (la
mitad temporal). NO formaliza la invalidación-por-evento ni el campo `vigencia`
normativa: sigue siendo relación `contradice`/`reemplaza` ad-hoc. Falta un gen de
vigencia normativa (o un estado `derogada` vía gen-entidad-con-estado, que hoy no
se aplica a normas).

---

## Regresión nueva detectada

- **Campo `vigencia:` huérfano.** Las páginas de normativa/jurisprudencia del run
  anterior llevan `vigencia: vigente|en-revision|no-vigente` en frontmatter, pero
  M1 v3 NO lo lista (ni en núcleo ni como opcional) y ningún gen lo define. Bajo el
  genoma nuevo, `gen-frontmatter-obligatorio` lo trataría como campo no reconocido;
  o se promueve a gen propio (cerrando la fricción 3) o se elimina. Hoy es ambiguo.
- **`gen-query` v1 sin editar:** mantiene "cita SIEMPRE las páginas-fuente", texto
  que contradice literalmente a M3. Se resuelve por precedencia pero conviene
  alinear la redacción (riesgo de regresión si alguien lee gen-query aislado).

---

## Resumen

| Fricción | Mutación | Veredicto |
|---|---|---|
| 1. Confidencialidad vs. apertura | M3 gen-confidencialidad | ✅ resuelto |
| 2. Relaciones tipadas (cita/se_apoya_en) | M1 frontmatter v3 + relation_types | ✅ resuelto (declarar verbos en manifiesto) |
| 3. Vigencia normativa ≠ decay | M2 gen-vigencia-temporal | 🟡 parcial (solo mitad temporal) |

**Overall: PARTIAL.** Dos de tres fricciones quedan bien resueltas por el genoma
nuevo; la vigencia normativa por evento sigue sin campo propio.
