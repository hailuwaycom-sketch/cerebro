---
run_id: 2026-06-25-f5c6000
sha: f5c6000acb7fc8159d9e5fc4365e692f773a5773
tree_clean: true
gene_version: gen-auto-auditoria v1
date: 2026-06-25
scenario: agencia
role: maker
---

# Snapshot — AUDIT run 2026-06-25-f5c6000

## Estado del repositorio
- **SHA**: `f5c6000acb7fc8159d9e5fc4365e692f773a5773`
- **Árbol limpio**: sí (`git status` vacío antes de este run)
- **Gen aplicado**: `gen-auto-auditoria` version 1
- **Fecha de auditoría**: 2026-06-25 (hoy)

## Alcance: archivos auditados en `sim/agencia/`

### Manifiesto
- `sim/agencia/company.yaml`

### Genoma aplicado (`genome-applied/`)
- `sim/agencia/genome-applied/company-profile.md`
- `sim/agencia/genome-applied/events.jsonl`
- `sim/agencia/genome-applied/gen-lead-a-cliente.md`
- `sim/agencia/genome-applied/gen-objecion-transversal.md`
- `sim/agencia/genome-applied/gen-precio-perecedero.md`

### Wiki — meta
- `sim/agencia/wiki/index.md`
- `sim/agencia/wiki/log.md`

### Wiki — procedural
- `sim/agencia/wiki/procedural/sops-ventas/sop-calificacion-y-cierre.md`

### Wiki — semantic
- `sim/agencia/wiki/semantic/casos-exito/caso-exito-dental-sonrisa.md`
- `sim/agencia/wiki/semantic/clientes/cliente-dental-sonrisa.md`
- `sim/agencia/wiki/semantic/herramientas/bot-captacion-leads.md`
- `sim/agencia/wiki/semantic/herramientas/make.md`
- `sim/agencia/wiki/semantic/herramientas/n8n.md`
- `sim/agencia/wiki/semantic/herramientas/openai.md`
- `sim/agencia/wiki/semantic/herramientas/twilio.md`
- `sim/agencia/wiki/semantic/herramientas/whatsapp-cloud-api.md`
- `sim/agencia/wiki/semantic/leads/lead-dental-sonrisa.md`
- `sim/agencia/wiki/semantic/leads/lead-inmobiliaria-vertice.md`
- `sim/agencia/wiki/semantic/objeciones/objecion-bot-malo.md`
- `sim/agencia/wiki/semantic/objeciones/objecion-precio-alto.md`
- `sim/agencia/wiki/semantic/precios/precio-vertice-PROP-2026-038.md`
- `sim/agencia/wiki/semantic/propuestas/propuesta-vertice-PROP-2026-038.md`

### Wiki — working
- `sim/agencia/wiki/working/followup-vertice-2026-05-16.md`

### Ref auxiliares (sin modificar)
- `sim/agencia/findings.md`
- `sim/agencia/query-demo.md`
- `sim/agencia/regresion.md`
- `sim/agencia/raw/` (4 fuentes — solo existencia verificada; `raw/` es inmutable)

## Gen de reglas auditado (solo lectura)
- `genome/genes/gen-auto-auditoria.md` — version 1 (fuera del sandbox; leído en modo solo lectura)

## Taxonomía declarada en `company.yaml`
```
semantic: [clientes, leads, propuestas, casos-exito, objeciones, precios, herramientas]
procedural: [sops-ventas, onboarding-cliente]
```
Categorías con cobertura verificada: clientes, leads, propuestas, casos-exito, objeciones, precios, herramientas, sops-ventas.
Categoría sin ninguna página: **onboarding-cliente** (vacío).
