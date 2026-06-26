---
run_id: 2026-06-25-f5c6000
scenario: produccion
empresa: Forjados del Valle S.A.
fecha_audit: 2026-06-25
rol: maker
gen_auditoria_version: 1
estado_git: sandbox-sim (no se aplica git-SHA al subtree sim/; run-id fijado por convención)
---

# 00 — Snapshot del sandbox `sim/produccion/`

## Alcance auditado

Únicamente `sim/produccion/` — ningún archivo fuera de este subtree fue modificado.

## Fuentes leídas (esqueleto)

### genome-applied/ (3 genes de sector)
| id | status | version | trigger (resumen) |
|---|---|---|---|
| gen-trazabilidad-lote | active | 1 | fuente menciona lote, OP o número de serie |
| gen-no-conformidad | active | 1 | registro de calidad con defecto/rechazo/desviación |
| gen-estado-maquina | active | 1 | fuente reporta estado, mantenimiento o avería de máquina |

### wiki/ — frontmatter skeleton (13 páginas)
| archivo | type | tier | decay_rate | valido_hasta | estado (campo) | sensibilidad |
|---|---|---|---|---|---|---|
| semantic/productos/soporte-motor-SM45 | entidad | semantic | low | — | — | — |
| semantic/lineas/L1-estampado | entidad | semantic | low | — | — | — |
| semantic/maquinas/PR-200-prensa | entidad | semantic | low | — | operativa | — |
| semantic/proveedores/AceroNorte | entidad | semantic | low | — | — | — |
| semantic/lotes/LOTE-SM45-2606 | entidad | semantic | low | — | — | — |
| semantic/lotes/OP-2026-0417 | fuente | semantic | medium | — | estado_op: cerrada | — |
| semantic/calidad/NCR-2026-031 | fuente | semantic | high | — | estado: cerrada | — |
| semantic/maquinas/MTTO-PR-200-2026-06 | fuente | semantic | high | — | — | — |
| procedural/sops-calidad/SOP-CAL-03 | sop | procedural | low | — | — | — |
| procedural/sops-mantenimiento/SOP-MTTO-01 | sop | procedural | low | — | — | — |
| index.md | meta | — | — | — | — | — |

### raw/ (3 fuentes — inmutables, solo lectura)
- `raw/OP-2026-0417.md`
- `raw/NCR-2026-031-calidad.md`
- `raw/MTTO-PR-200-2026-06.md`

### Otros archivos del sandbox (contexto)
- `company.yaml` — manifiesto ONBOARD (taxonomía, seed_genes, glossary, roles)
- `findings.md` — resultado previo de ciclo EVOLVE
- `regresion.md` — verificación de regresión contra genoma nuevo
- `query-demo.md` — no leído en skeleton (no marcado por detectores)

## Grafo de relaciones (resumen)
```
OP-2026-0417 → genero_lote → LOTE-SM45-2606
LOTE-SM45-2606 → producido_en → PR-200-prensa
                → bajo_orden  → OP-2026-0417
                → de_producto → soporte-motor-SM45
                → insumo_de   → AceroNorte
                → afectado_por → NCR-2026-031
NCR-2026-031 → afecta_a   → LOTE-SM45-2606
             → viola       → SOP-CAL-03-inspeccion-dimensional
             → causa_en    → PR-200-prensa
             → mitiga_con  → MTTO-PR-200-2026-06
MTTO-PR-200-2026-06 → mantiene_a → PR-200-prensa
                     → resuelve   → NCR-2026-031
PR-200-prensa → intervenida_por → MTTO-PR-200-2026-06
               → produjo_lote   → LOTE-SM45-2606
SOP-MTTO-01 → aplica_a → PR-200-prensa
             → reforzado_por → MTTO-PR-200-2026-06
SOP-CAL-03  → aplica_a → soporte-motor-SM45
             → violado_por → NCR-2026-031
```

## Confidencialidad
Ninguna página del sandbox declara `sensibilidad: confidencial`. No hay restricciones de
transcripción para las evidencias de este run.

## Flags del skeleton (pre-detección)
Los siguientes ítems se marcaron para drill-down antes del análisis de detección:

1. **SOP-MTTO-01** — describe una brecha ("checklist NO incluye verificación del tope de
   altura") sin que el SOP haya sido actualizado formalmente. El raw de MTTO confirma la
   recomendación, pero el cuerpo del SOP aún refleja el checklist incompleto.
2. **NCR-2026-031 / gen-no-conformidad** — el gen exige enlace `viola` O `mitiga_con`; la NCR
   tiene ambos (`viola: SOP-CAL-03`, `mitiga_con: MTTO-PR-200-2026-06`) pero le falta el
   enlace inverso `causa_en` → confirmado presente. Sin embargo el gen no menciona el campo
   `causa_en`; relación de facto pero no en esquema del gen.
3. **MTTO-PR-200-2026-06** — `relations.usa` apunta a `[[AceroNorte]]`, que semánticamente
   es "repuesto consumido de" (rol de proveedor de utillaje), no el significado genérico de
   `usa`. Posible uso incorrecto del verbo de núcleo.
4. **company.yaml** — no declara `relation_types` pese a que las páginas usan 14 verbos de
   dominio fuera del núcleo `{usa, depende_de, contradice, reemplaza}`.
5. **Páginas de tipo `evento`** (NCR, MTTO, OP) — ninguna declara el campo `clase:` exigido
   por `gen-clase-temporal` (que forma parte del genoma nuevo referenciado en regresion.md).
6. **PR-200-prensa** — `proximo_preventivo: 2026-07-19` existe como campo informativo, no
   como `valido_hasta`. Hoy es 2026-06-25; no está vencido, pero el campo no es auditable por
   `gen-vigencia-temporal`.
