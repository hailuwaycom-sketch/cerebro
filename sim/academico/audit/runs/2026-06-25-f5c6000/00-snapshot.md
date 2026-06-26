---
run_id: 2026-06-25-f5c6000
scenario: academico
role: snapshot
fecha: 2026-06-25
gen_auditoria_version: 1
estado_repo: sandbox-sim (no hay HEAD real; archivos aislados bajo sim/academico/)
---

# 00-Snapshot — Escenario académico · Run 2026-06-25-f5c6000

Insumos leídos en modo **esqueleto** (frontmatter + relaciones); drill-down solo en páginas flagueadas.

## Fuentes leídas

| Ruta | Tipo | Tier |
|---|---|---|
| `sim/academico/company.yaml` | manifiesto | — |
| `sim/academico/genome-applied/gen-cita-trazable.md` | gen sembrado | — |
| `sim/academico/genome-applied/gen-version-paper.md` | gen sembrado | — |
| `sim/academico/wiki/index.md` | meta | — |
| `sim/academico/wiki/semantic/papers/wp-2024-07.md` | working-paper | semantic |
| `sim/academico/wiki/semantic/papers/giea-2025-03.md` | paper-publicado | semantic |
| `sim/academico/wiki/semantic/papers/preprint-replica-rios-2023.md` | preprint | semantic |
| `sim/academico/wiki/semantic/datasets/enph-2022-giea.md` | dataset | semantic |
| `sim/academico/wiki/semantic/datasets/enph-2022-v2-giea.md` | dataset | semantic |
| `sim/academico/wiki/semantic/revisiones/revision-par-giea-2025-03.md` | revision-par | semantic |
| `sim/academico/wiki/semantic/investigadores/valentina-rios.md` | entidad | semantic |
| `sim/academico/wiki/semantic/investigadores/carlos-mendoza.md` | entidad | semantic |
| `sim/academico/wiki/semantic/investigadores/sofia-paredes.md` | entidad | semantic |
| `sim/academico/wiki/semantic/proyectos/proyecto-fp-044-2023.md` | entidad | semantic |
| `sim/academico/raw/wp-2024-07-elasticidad-demanda-energia.md` | raw (solo lectura) | — |
| `sim/academico/raw/enph-2022-readme.md` | raw (solo lectura) | — |

## Grafo de relaciones (esqueleto)

```
giea-2025-03
  supersede  → wp-2024-07
  cita       → enph-2022-giea, enph-2022-v2-giea
  revisado_por → revision-par-giea-2025-03

wp-2024-07
  cita       → enph-2022-giea

preprint-replica-rios-2023
  cita       → [[metodologia-iva-2016]]   ← PÁGINA INEXISTENTE
  replica    → [[metodologia-iva-2016]]   ← PÁGINA INEXISTENTE

enph-2022-v2-giea
  deriva_de  → enph-2022-giea

revision-par-giea-2025-03
  sensibilidad: confidencial
  deriva_de  → giea-2025-03
```

## Taxonomía vs. cobertura real

| Categoría declarada (company.yaml) | Páginas existentes |
|---|---|
| investigadores | valentina-rios, carlos-mendoza, sofia-paredes ✓ |
| papers | wp-2024-07, giea-2025-03, preprint-replica-rios-2023 ✓ |
| datasets | enph-2022-giea, enph-2022-v2-giea ✓ |
| proyectos | proyecto-fp-044-2023 ✓ |
| revisiones | revision-par-giea-2025-03 ✓ |
| **convenios** | **ninguna** ← VACÍO ESTRUCTURAL |

## Flags del esqueleto (señales para drill-down)

1. **Contradicción numérica**: `wp-2024-07` reporta elasticidad −0.8; `giea-2025-03` (mismo estudio, publicado) reporta −0.5. Ambas páginas activas.
2. **Obsolescencia**: `wp-2024-07` tiene `tag: supersedido` pero permanece en `semantic/` sin degradación de tier.
3. **Link roto**: `preprint-replica-rios-2023` → `cita` + `replica` a `[[metodologia-iva-2016]]`; página inexistente.
4. **Redundancia de datasets**: `enph-2022-giea` y `enph-2022-v2-giea` — misma fuente raw, misma fecha descarga, confidences idénticas; relación `deriva_de` presente pero sin documentar cuál es canónica.
5. **Vacío estructural**: categoría `convenios` declarada en manifiesto, cero páginas.
6. **Confidencial**: `revision-par-giea-2025-03` (`sensibilidad: confidencial`) — referenciada por `[[link]]`/campos únicamente en los artefactos de auditoría; nunca se transcribe identidad ni recomendación.

## Páginas fuera del drill-down (sin señales)

`valentina-rios`, `carlos-mendoza`, `sofia-paredes`, `proyecto-fp-044-2023` — frontmatter limpio, relaciones coherentes, `valido_hasta` vigente donde aplica. No se leen en detalle.
