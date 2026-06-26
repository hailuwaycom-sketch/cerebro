---
run_id: 2026-06-25-f5c6000
role: snapshot
scenario: legal
company: "Vega & Alcántara Abogados"
date: 2026-06-25
gen_auditoria_version: 1
---

# 00-snapshot — Audit run 2026-06-25-f5c6000 (sandbox: legal)

## Identidad del estado

- Scenario path: `sim/legal/`
- Isolation: solo archivos bajo `sim/legal/` — no se toca real `wiki/`, real `genome/`, ni otros `sim/<otro>`.
- Gen aplicado: `genome/genes/gen-auto-auditoria.md` (version 1) — lectura únicamente.
- Fecha auditoria: 2026-06-25
- run-id: `2026-06-25-f5c6000` (identificador manual para sandbox; sin SHA limpio porque el árbol real es sin cambios pero este run no se commitea).

## Taxonomía declarada (`company.yaml`)

| Tier | Carpetas |
|---|---|
| semantic | clientes, casos, contrapartes, contratos, jurisprudencia, normativa, dictamenes |
| procedural | sops-litigio, plantillas-clausulas |

- Genes sembrados: `gen-secreto-profesional`, `gen-version-contrato`, `gen-conflicto-interes`
- Tipos de documento: contrato, escrito-de-caso, jurisprudencia, dictamen, minuta-cliente

## Corpus auditado (esqueleto)

### genome-applied/ (3 genes)

| Archivo | id | status | version |
|---|---|---|---|
| gen-secreto-profesional.md | gen-secreto-profesional | active | 1 |
| gen-version-contrato.md | gen-version-contrato | active | 1 |
| gen-conflicto-interes.md | gen-conflicto-interes | active | 1 |

### wiki/ (18 páginas)

| Ruta | type | tier | tags clave | sensibilidad/secreto_prof | vigencia | relations (resumen) |
|---|---|---|---|---|---|---|
| wiki/index.md | meta | semantic | — | — | — | lista raíz |
| wiki/semantic/casos/caso-exp-2026-0142.md | entidad | semantic | caso, confidencial | secreto_profesional: true | — | usa v3, jurisprud.; depende_de: cliente, contraparte, juzgado |
| wiki/semantic/casos/minuta-2026-05-12-andina.md | fuente | semantic | minuta, confidencial | secreto_profesional: true | — | depende_de: caso, cliente |
| wiki/semantic/clientes/distribuidora-andina.md | entidad | semantic | cliente, confidencial | secreto_profesional: true | — | usa: caso |
| wiki/semantic/contrapartes/logistica-del-norte.md | entidad | semantic | contraparte | false | — | (ninguna saliente) |
| wiki/semantic/contratos/contrato-distribucion-v2.md | fuente | semantic | contrato, historico | false | — | depende_de: caso; reemplaza: [] |
| wiki/semantic/contratos/contrato-distribucion-v3.md | fuente | semantic | contrato, vigente | false | — | usa: jurisprud.; depende_de: caso; reemplaza: v2 |
| wiki/semantic/jurisprudencia/jurisprudencia-moderacion-clausula-penal.md | concepto | semantic | jurisprudencia, precedente | false | en-revision | usa: art-1154, art-1124 |
| wiki/semantic/normativa/art-1154-codigo-civil.md | concepto | semantic | normativa, vigente | false | vigente | (ninguna saliente) |
| wiki/semantic/normativa/art-1124-codigo-civil.md | concepto | semantic | normativa, vigente | false | vigente | (ninguna saliente) |
| wiki/semantic/normativa/reforma-cc-2026.md | concepto | semantic | normativa, no-vigente, alerta-vigencia | false | no-vigente | contradice: art-1154, jurisprud. |
| wiki/semantic/casos/juzgado-mercantil-3-madrid.md | entidad | semantic | juzgado | false | — | (ninguna saliente) |
| wiki/semantic/casos/lucia-vega.md | entidad | semantic | abogado | false | — | usa: caso |
| wiki/semantic/casos/mateo-alcantara.md | entidad | semantic | abogado | false | — | usa: jurisprud. |
| wiki/procedural/plantillas-clausulas/clausula-penalizacion-demora.md | sop | procedural | plantilla | false | — | usa: jurisprud. |
| wiki/procedural/sops-litigio/sop-requerimiento-cumplimiento.md | sop | procedural | sop | false | — | usa: art-1124 |

### Taxonomía vs. carpetas reales

| Esperado en taxonomy | Presente | Estado |
|---|---|---|
| semantic/clientes | sí | ok |
| semantic/casos | sí (también usada para abogados y juzgado) | ok / misuse menor |
| semantic/contrapartes | sí | ok |
| semantic/contratos | sí | ok |
| semantic/jurisprudencia | sí | ok |
| semantic/normativa | sí | ok |
| semantic/dictamenes | NO | vacío — sin página |
| procedural/sops-litigio | sí | ok |
| procedural/plantillas-clausulas | sí | ok |

### Flags de primer pase (candidatos potenciales)

1. **`contrato-distribucion-v2` `sources` apunta a raw v3** — la fuente citada en la página histórica (v2) es la misma que la de v3, no la fuente original de v2.
2. **`vigencia:` campo huérfano** — aparece en páginas de normativa/jurisprudencia pero no está definido en ningún gen activo del sandbox (ni en genome-applied/).
3. **`jurisprudencia-moderacion-clausula-penal` contradice:[]** — la página no declara explícitamente que `reforma-cc-2026` la contradice; solo `reforma-cc-2026` lo declara de su lado.
4. **Carpeta `semantic/dictamenes` ausente** — taxonomy la declara, no existe.
5. **`contrato-distribucion-v2` `relations.reemplaza:[]`** — según `gen-version-contrato`, la versión histórica debería poder referenciarse mediante un campo `reemplazada_por` o al menos su `reemplaza` debería estar vacío y ser v3 quien lo declare. v3 sí lo hace (`reemplaza: [v2]`). Consistencia asimétrica (v2 no apunta de vuelta).
6. **Abogados `lucia-vega` y `mateo-alcantara` en carpeta `casos/`** — la taxonomy no asigna carpeta para personas/equipo; están en `casos/` por defecto, lo que mezcla tipos.
7. **`minuta-2026-05-12-andina` enlazada desde `caso-exp-2026-0142.md` (body text y relation indirecta)** — confirmar si eso viola `gen-secreto-profesional` (index no la enlaza, pero caso sí, lo cual es correcto por el gen).

## Insumos para Maker

- Regla rúbrica: `impacto = severidad*10 + alcance` (alcance = nº páginas/genes afectados).
- Hoy = 2026-06-25. No hay `valido_hasta` en páginas wiki; campo `vigencia` presente en algunas.
- Confidencialidad: páginas con `secreto_profesional: true` → [[caso-exp-2026-0142]], [[minuta-2026-05-12-andina]], [[distribuidora-andina]]. Evidencia por link/id + campo únicamente.
