---
run_id: 2026-06-25-dc198a0
role: orchestrator
date: 2026-06-25
gene_version: "gen-auto-auditoria v1"
candidates_found: 8
candidates_confirmed: 8
proposals: 3
---

# Proposals — orquestador (2026-06-25-dc198a0)

Run-id: `2026-06-25-dc198a0`. El maker encontró 8 candidatos; el auditor confirmó los 8/8 (0
refutados). Las siguientes 3 propuestas son el top-3 por impacto tras el desempate de clase
(C1 > C2 por prioridad de clase; C3 es única en su impacto). Aguardan decisión humana.

---

## P1

```
id: P1
fecha: 2026-06-25
motivo: Contradicción entre dos genes activos con trigger idéntico y reglas mutuamente
  excluyentes — [[gen-fix-precio-abierto]] ordena citar siempre el precio sin importar
  fecha; [[gen-fix-precio-vigencia]] prohíbe citar precios vencidos. La colisión produce
  comportamiento indeterminado ante cualquier fuente con precios con fecha de vigencia.
evidencia: >
  [[gen-fix-precio-abierto]] (status: active, trigger: "la fuente menciona un precio") y
  [[gen-fix-precio-vigencia]] (status: active, mismo trigger) tienen reglas mutuamente
  excluyentes. [[gen-fix-precio-vigencia]] lo declara explícitamente: "Contradice
  directamente a [[gen-fix-precio-abierto]]". Ningún valor sensible involucrado.
diff: >
  Deprecar [[gen-fix-precio-abierto]] (status: deprecated, superado_por:
  gen-fix-precio-vigencia); subir version de [[gen-fix-precio-vigencia]] a 2; registrar
  en genome/events.jsonl.
score: 52
status: pending
```

---

## P2

```
id: P2
fecha: 2026-06-25
motivo: Protocolo de seguridad [[protocolo-bloqueo-loto]] vencido hace ~6 meses
  (valido_hasta: 2026-01-01) y aún referenciado como guía normativa vigente por
  [[prensa-p1]] en relations.tratada_segun. Operar con una guía de seguridad caducada
  constituye riesgo operativo en dominio de seguridad.
evidencia: >
  [[protocolo-bloqueo-loto]] tiene valido_hasta: 2026-01-01 y tags: [seguridad, protocolo];
  hoy 2026-06-25 → vencido ~6 meses. [[prensa-p1]] lo referencia en
  relations.tratada_segun como fuente normativa activa, propagando el riesgo a esa entidad.
diff: >
  En [[protocolo-bloqueo-loto]]: cambiar decay_rate a expired (o añadir estado: vencido)
  y agregar advertencia en cabecera. En [[prensa-p1]]: añadir nota en
  relations.tratada_segun indicando que el protocolo está vencido hasta revalidación.
score: 52
status: pending
```

---

## P3

```
id: P3
fecha: 2026-06-25
motivo: Contradicción sin resolver entre el estado del cliente Acme en dos páginas wiki
  activas — [[cliente-acme]] declara estado: activo con contrato vigente; [[caso-acme]]
  registra la baja de Acme en mayo 2026 y declara relations.contradice: ["[[cliente-acme]]"].
evidencia: >
  [[cliente-acme]] (estado: activo, "contrato vigente") y [[caso-acme]] ("dado de baja
  en mayo 2026", relations.contradice: ["[[cliente-acme]]"]). La contradicción es
  explícita y bidireccional; ninguna de las dos páginas ha sido resuelta o archivada.
diff: >
  En [[cliente-acme]]: actualizar estado: baja, last_reinforced: 2026-05-20, y añadir
  relations.contradice_resuelta: ["[[caso-acme]]"]. La fuente de verdad pasa a ser
  [[caso-acme]].
score: 42
status: pending
```
