# regresion.md — REGRESIÓN del sandbox SALUD (Clínica Vida Serena)

Verificación de si el **genoma NUEVO (16 genes)** resuelve las 3 fricciones que
`findings.md` reportó contra el **genoma BASE (10 genes)**. No se reconstruye el escenario:
se re-deriva el frontmatter de casos ya existentes en el sandbox aplicando los genes nuevos.
Solo lectura fuera de `sim/salud/`.

Genes nuevos relevantes (`D:/cerebro/genome/genes/`):
- M1 `gen-frontmatter-obligatorio` **v3**: `relations` deja de ser set cerrado; núcleo
  `{usa, depende_de, contradice, reemplaza}` ∪ `relation_types` declarados en `company.yaml`.
  Campos opcionales: `valido_hasta`, `sensibilidad`, `clase`/`fecha_evento`/`volatile_fields`, `estado`.
- M2 `gen-vigencia-temporal`: `valido_hasta` duro; QUERY advierte SIEMPRE; en dominios de
  seguridad (salud/legal) una página vencida sin `reemplaza` se eleva como hallazgo PRIORITARIO de LINT.
- M3 `gen-confidencialidad`: eje `sensibilidad: publico|interno|confidencial`; `confidencial`
  no se ancla en index, no se promueve/fusiona, QUERY no la cita textualmente; INGEST se detiene ante PII real.
- Apoyo: `gen-clase-temporal` (`clase: estable|evento`) para incidente/episodio.

---

## Fricción 1 — PII/confidencialidad (¿M3?)

Caso: página de paciente `wiki/semantic/pacientes/PAC-7731.md` (fuente
`raw/HC-EP-2026-0488-anonimizada.md`, ID seudonimizado, sin nombre real).

Re-derivación del frontmatter con M3 (vocabulario canónico del gen base):
```yaml
sensibilidad: confidencial      # M3 — antes inexistente en genoma base
pii: anonimizado                # ya presente vía seed-gen; M3 lo formaliza como eje transversal
clase: evento                   # gen-clase-temporal: episodio fechado
fecha_evento: 2026-06-21
relations:
  presenta_patologia: ["[[anafilaxia]]"]
  tratado_con: ["[[adrenalina]]"]
  segun_protocolo: ["[[protocolo-anafilaxia]]"]
  atendido_por: ["[[dra-soto-urg]]"]
```
Efecto de M3: la página NO se ancla en `index.md`, CONSOLIDATE NO la promueve de tier ni la
fusiona con la ficha abierta de [[anafilaxia]], y QUERY responde con referencia indirecta
(ID seudonimizado), sin exponer edad/sexo/triaje. INGEST se habría detenido si la fuente
trajera nombre+documento reales. El conocimiento clínico generalizable ya vive separado en
las fichas de patología/protocolo; el nodo de paciente solo enlaza.

**Veredicto: ✅ RESUELTO.** El riesgo "promover/fusionar a un paciente" queda cerrado por
regla explícita; INGEST tiene parada dura ante PII. (Nota menor: la página usa `confidential: true`
en vez del eje `sensibilidad: confidencial` del gen base — equivalencia semántica, no funcional;
es deuda de nomenclatura del seed-gen, no una fricción.)

## Fricción 2 — caducidad dura vs. confianza blanda (¿M2?)

Caso: protocolo `wiki/procedural/protocolos-clinicos/protocolo-anafilaxia.md` (v3,
fuente `raw/PROT-ANAFILAXIA-v3-2024.md`, vence 2026-03-01; hoy 2026-06-22 → vencido) y su
incidente [[INC-2026-014]].

Re-derivación con M2:
```yaml
valido_hasta: 2026-03-01        # duro, independiente de confidence/decay_rate
status: vencido                 # 2026-03-01 < 2026-06-22
decay_rate: high
relations:
  reemplaza: []                 # v4 NO publicada -> sin reemplazo
```
Hoy (2026-06-22) `valido_hasta` ya pasó y NO hay página que lo `reemplaza` → M2 lo eleva
como **hallazgo PRIORITARIO de LINT** en dominio de seguridad (no como simple baja de
`confidence`). QUERY antepone SIEMPRE la advertencia de caducidad antes de citarlo. Esto es
exactamente el escenario que originó el cuasi-incidente INC-2026-014: el genoma nuevo lo
habría señalado como peligro accionable, no como dato "menos confiable". La distinción
decaimiento estadístico vs. caducidad normativa queda formalizada.

**Veredicto: ✅ RESUELTO.** Salvedad: M2 vive en `gen-vigencia-temporal`, pero `gen-lint`
sigue en **v1** (su texto solo menciona "vencidas según last_reinforced+decay_rate", no
`valido_hasta`). La elevación prioritaria es correcta porque M2 la enuncia, pero LINT no la
referencia cruzada → acoplamiento implícito (ver regresiones).

## Fricción 3 — relaciones tipadas extensibles (¿M1?)

Caso: tríada `adrenalina` ↔ `anafilaxia` ↔ `protocolo-anafilaxia` (+ aristas de paciente e
incidente). Verbos en uso: `presenta_patologia, tratado_con, segun_protocolo, atendido_por,
trata, indica, tratamiento_de, indicado_por, tratada_segun, afecta_a, relacionado_con,
reportado_por`.

M1 v3 ya declara que `relations` NO es set cerrado y que LINT valida contra la **unión**
núcleo ∪ `relation_types` del `company.yaml`. El contrato del gen, por tanto, SÍ admite las
aristas clínicas. PERO: `sim/salud/company.yaml` **no declara la clave `relation_types`**
(verificado: la sección no existe; el `company.example.yaml` sí la trae). Sin esa
declaración, la "unión" se reduce al núcleo `{usa, depende_de, contradice, reemplaza}` y LINT
marcaría los 12 verbos clínicos como **relaciones no declaradas** (falsos positivos masivos).

**Veredicto: 🟡 PARCIAL.** El mecanismo existe y es el correcto (resuelve el problema de raíz),
pero queda inerte en este sandbox porque el manifiesto de salud no fue actualizado para
declarar sus `relation_types`. Fix de un renglón en `company.yaml`:
```yaml
relation_types: [presenta_patologia, tratado_con, segun_protocolo, atendido_por,
                 trata, indica, tratamiento_de, indicado_por, tratada_segun,
                 afecta_a, relacionado_con, reportado_por]
```
Con esa línea, M1 pasa a ✅. Sin ella, las aristas siguen "fuera del contrato" igual que en
el genoma base.

---

## Veredicto global
- F1 (PII): ✅  ·  F2 (caducidad dura): ✅  ·  F3 (relaciones tipadas): 🟡 (mecanismo OK, manifiesto sin actualizar).
- **Overall: PARTIAL** — 2/3 resueltas; la 3ª solo por falta de declaración en `company.yaml`, no por defecto del gen.

## Regresiones nuevas introducidas por los genes nuevos
1. **Desincronía M1↔manifiesto:** `gen-frontmatter v3` delega la validez de relaciones a
   `company.yaml:relation_types`, pero el `company.yaml` de salud no migró esa clave. Antes
   las aristas "no se validaban" (laguna); ahora "se validarían como inválidas" si el
   manifiesto no se completa — regresión latente, no de comportamiento ya observado.
2. **`gen-lint` quedó en v1 (sin M2):** la lógica de `valido_hasta` + elevación prioritaria
   vive en `gen-vigencia-temporal`, pero el texto de LINT no la cita. Funciona por
   solapamiento, pero el contrato de LINT está desactualizado → riesgo de que un agente que
   lea solo `gen-lint` no aplique la caducidad dura.
3. **Deuda de nomenclatura (no funcional):** las páginas wiki usan `confidential:`/`valid_until:`
   (inglés, seed-gen) y los genes base usan `sensibilidad:`/`valido_hasta:` (español).
   Semánticamente equivalentes; conviene unificar para que LINT no las trate como campos distintos.
