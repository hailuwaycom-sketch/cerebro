---
title: CEREBRO produccion — índice (sandbox)
type: meta
updated: 2026-06-22
---

# Índice — Forjados del Valle S.A. (sandbox manufactura)

Punto de entrada. Navegar desde aquí por relaciones `[[...]]`.

## Genoma del sector (sembrado por ONBOARD)
- [[gen-trazabilidad-lote]] · [[gen-no-conformidad]] · [[gen-estado-maquina]]

## Anclas por tier
### semantic/
- **productos** — [[soporte-motor-SM45]]
- **lineas** — [[L1-estampado]]
- **maquinas** — [[PR-200-prensa]]
- **lotes** — [[LOTE-SM45-2606]] · [[OP-2026-0417]]
- **calidad** — [[NCR-2026-031]]
- **proveedores** — [[AceroNorte]]
- (eventos de mantenimiento) — [[MTTO-PR-200-2026-06]]

### procedural/
- **sops-calidad** — [[SOP-CAL-03-inspeccion-dimensional]]
- **sops-mantenimiento** — [[SOP-MTTO-01-preventivo-prensas]]

## Grafo de trazabilidad (ejemplo)
`OP-2026-0417 → LOTE-SM45-2606 → {PR-200-prensa, soporte-motor-SM45, AceroNorte}`
`NCR-2026-031 → afecta LOTE-SM45-2606 → causa en PR-200 → mitiga MTTO-PR-200-2026-06`
