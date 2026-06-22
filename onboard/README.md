# ONBOARD reproducible

`ONBOARD` adapta el cerebro genérico a una empresa concreta **de forma reproducible**.
La fuente de verdad es un manifiesto declarativo: `onboard/company.yaml`.

> Regla de oro: **mismo `company.yaml` → mismo genoma.** La parte no determinista (una
> entrevista) solo sirve para *generar* el manifiesto; el aplicado es una función pura.

## Tres modos (todos reproducibles)
1. **Manifiesto** — copia `company.example.yaml` a `company.yaml`, edítalo y aplica `ONBOARD`.
2. **Blueprint** — elige una receta de `onboard/blueprints/<sector>.yaml`, cópiala a
   `company.yaml` y aplica. Onboardea muchas empresas del mismo vertical igual.
3. **Entrevista** — el agente te pregunta, **escribe primero** `company.yaml` y luego aplica.

## Qué hace el aplicado (determinista e idempotente)
1. Lee `company.yaml`.
2. Completa `genome/company-profile.md` (`status: configurado`).
3. Crea la taxonomía de carpetas de `taxonomy:` en `wiki/semantic/` y `wiki/procedural/`.
4. Siembra cada `seed_genes` en `genome/genes/` pasando por `gen-compuerta-mutacion`,
   registrando una línea por gen en `genome/events.jsonl` + commit.
5. Actualiza `index.md` con la empresa y las anclas iniciales.
6. Re-sincroniza `AGENTS.md`. **No ingiere contenido**: ONBOARD solo configura.

Reaplicar el mismo manifiesto no duplica nada; reaplicar uno editado genera un diff limpio.
