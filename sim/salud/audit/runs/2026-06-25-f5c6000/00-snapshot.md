---
run_id: 2026-06-25-f5c6000
scenario: salud
role: snapshot
date: 2026-06-25
gen_version: gen-auto-auditoria v1
state: sandbox (no git SHA — sim/ aislado)
mode: TUNING / MAKER
---

# 00-snapshot — Clínica Vida Serena (sandbox salud)

> Insumo inmutable para las pasadas Maker y Auditor. No se modifica tras la escritura.

## Identidad del estado

- Escenario: `sim/salud/` (sandbox aislado; no se usan git SHA del repo principal)
- Fecha de auditoría: **2026-06-25**
- Gen aplicado: `gen-auto-auditoria` versión **1**
- Modo: TUNING — base realista, sin oráculo, evaluación honesta de fricciones del gen

## Corpus auditado

### genome-applied/ (genes activos del sector)

| id | status | version | trigger (resumen) |
|---|---|---|---|
| gen-confidencialidad-paciente | active | 1 | fuente con datos de paciente (HC, episodio, id) |
| gen-vigencia-protocolo | active | 1 | protocolo clínico, GPC o SOP asistencial |
| gen-trazabilidad-clinica | active | 1 | relación fármaco ↔ patología ↔ protocolo |

Tres genes activos. Sin solapamiento de trigger aparente al primer escaneo.

### wiki/ — inventario por tier

**procedural/**

| archivo | type | status | valid_until | confidence | relaciones clave |
|---|---|---|---|---|---|
| protocolos-clinicos/protocolo-anafilaxia.md | sop | **vencido** | 2026-03-01 | 0.55 | usa+indica→[[adrenalina]], trata→[[anafilaxia]], reemplaza:[] |

**semantic/**

| archivo | type | confidential | decay_rate | relaciones clave |
|---|---|---|---|---|
| patologias/anafilaxia.md | concepto | — | low | tratada_segun→[[protocolo-anafilaxia]], tratamiento_de→[[adrenalina]] |
| farmacos/adrenalina.md | entidad | — | low | tratamiento_de→[[anafilaxia]], indicado_por→[[protocolo-anafilaxia]] |
| pacientes/PAC-7731.md | entidad | **true** | medium | presenta_patologia→[[anafilaxia]], tratado_con→[[adrenalina]], segun_protocolo→[[protocolo-anafilaxia]] |
| incidentes/INC-2026-014.md | entidad | — | high | afecta_a→[[protocolo-anafilaxia]], relacionado_con→[[PAC-7731]], reportado_por→[[enf-rivas]] |
| profesionales/dra-soto-urg.md | entidad | — | low | atiende→[[PAC-7731]], aprueba→[[protocolo-anafilaxia]] |
| profesionales/enf-rivas.md | entidad | — | low | reporta→[[INC-2026-014]] |

**Páginas en wiki:** 7 (1 procedural + 6 semantic). `company-profile.md` + `index.md` = 2 meta.

### Taxonomía declarada vs cobertura wiki

De `company.yaml`:

| entidad/taxonomía | declaradas | con página wiki | cobertura |
|---|---|---|---|
| pacientes | — (sensible) | 1 (PAC-7731) | N/A |
| profesionales | dr-nunez-mi, dra-soto-urg, enf-rivas | 2 de 3 | parcial — dr-nunez-mi sin página |
| protocolos | sepsis-adulto, dolor-toracico, anafilaxia | 1 de 3 | 33 % |
| farmacos | adrenalina, amoxicilina-clavulanico, noradrenalina | 1 de 3 | 33 % |
| patologias | sepsis, anafilaxia, neumonia-adquirida-comunidad | 1 de 3 | 33 % |
| incidentes | — | 1 (INC-2026-014) | N/A |

**Cobertura global del conocimiento declarado: muy baja** (mayoría de entidades sin página).

## Alertas del index.md (pre-existentes)

- `⚠️ [[protocolo-anafilaxia]] status: vencido (valid_until 2026-03-01) — sin reemplazo v4.`

## Parámetros de la rúbrica (gen-auto-auditoria v1)

```
impacto = severidad × 10 + alcance
alcance = nº páginas/genes afectados
```

| clase | severidad |
|---|---|
| contradicción entre genes activos | 5 |
| info vencida en dominio de seguridad | 5 |
| contradicción entre páginas wiki | 4 |
| regla obsoleta/deprecable | 3 |
| vacío (link roto / categoría sin cobertura) | 2 |
| redundancia (duplicado) | 2 |

Desempate: impacto → prioridad de clase → ruta alfabética.
