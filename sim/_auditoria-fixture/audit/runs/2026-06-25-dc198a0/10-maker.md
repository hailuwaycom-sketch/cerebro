---
run_id: 2026-06-25-dc198a0
role: maker
date: 2026-06-25
gene_version: "gen-auto-auditoria v1"
candidates: 8
---

# Maker — candidatos de auditoría (2026-06-25-dc198a0)

Método aplicado: esqueleto pass (frontmatter + relaciones de los 13 archivos), seguido de
drill-down completo sobre todos los archivos marcados por los detectores.
Fecha de referencia: 2026-06-25. Rúbrica: `impacto = severidad*10 + alcance`.

---

## C1 — Contradicción entre genes activos: precio abierto vs precio con vigencia

- **Clase:** contradicción entre genes activos
- **Genes afectados:** [[gen-fix-precio-abierto]], [[gen-fix-precio-vigencia]]
- **Evidencia:** Ambos genes tienen `status: active` y `trigger` idéntico (`la fuente menciona
  un precio`). Sus reglas son mutuamente excluyentes: [[gen-fix-precio-abierto]] ordena citar
  el precio siempre sin importar su fecha; [[gen-fix-precio-vigencia]] prohíbe citar precios
  vencidos. El propio texto de [[gen-fix-precio-vigencia]] declara: "Contradice directamente
  a [[gen-fix-precio-abierto]]".
- **Severidad:** 5
- **Alcance:** 2 (dos genes activos)
- **Impacto:** 52
- **Diff propuesto:** Deprecar [[gen-fix-precio-abierto]] (`status: deprecated`) y añadir
  `superado_por: gen-fix-precio-vigencia` en su frontmatter; subir `version` de
  [[gen-fix-precio-vigencia]] a 2 y registrar en `genome/events.jsonl`.

---

## C2 — Info vencida en dominio de seguridad

- **Clase:** info vencida en dominio de seguridad
- **Páginas afectadas:** [[protocolo-bloqueo-loto]], [[prensa-p1]]
- **Evidencia:** [[protocolo-bloqueo-loto]] tiene `valido_hasta: 2026-01-01` (vencido hace
  ~6 meses respecto a 2026-06-25) y pertenece al dominio de seguridad (`tags: [seguridad,
  protocolo]`). [[prensa-p1]] lo cita en `relations.tratada_segun`, propagando el riesgo de
  operar con un protocolo vencido.
- **Severidad:** 5
- **Alcance:** 2 (la página vencida + la página que la cita)
- **Impacto:** 52
- **Diff propuesto:** En [[protocolo-bloqueo-loto]] cambiar `decay_rate` a `expired` (o
  añadir `estado: vencido`) y agregar advertencia en cabecera; en [[prensa-p1]] agregar nota
  en `relations.tratada_segun` que el protocolo está vencido hasta revalidación.

---

## C3 — Contradicción entre páginas wiki: estado del cliente Acme

- **Clase:** contradicción entre páginas wiki
- **Páginas afectadas:** [[cliente-acme]], [[caso-acme]]
- **Evidencia:** [[cliente-acme]] declara `estado: activo` y describe a Acme como cliente con
  "contrato vigente". [[caso-acme]] registra la baja de Acme en mayo 2026 y tiene
  `relations.contradice: ["[[cliente-acme]]"]` explícito en su frontmatter, confirmando la
  colisión. La contradicción no está resuelta: ambas páginas permanecen activas.
- **Severidad:** 4
- **Alcance:** 2 (las dos páginas en conflicto)
- **Impacto:** 42
- **Diff propuesto:** En [[cliente-acme]] actualizar `estado: baja`, `last_reinforced:
  2026-05-20`, y añadir `relations.contradice_resuelta: ["[[caso-acme]]"]`; la fuente de
  verdad pasa a ser [[caso-acme]].

---

## C4 — Regla obsoleta/deprecable: gen-fix-clasifica-v1 subsumido por v2

- **Clase:** regla obsoleta/deprecable
- **Genes afectados:** [[gen-fix-clasifica-v1]], [[gen-fix-clasifica-v2]]
- **Evidencia:** Ambos genes tienen `status: active` y `trigger` idéntico (`la fuente es una
  ficha de producto`). [[gen-fix-clasifica-v2]] es un superconjunto estricto de v1 (añade
  campos SKU, material, proveedor y enlace) y su texto declara explícitamente que v1 "queda
  obsoleto (mismo trigger, regla subsumida)". Tener dos genes activos con trigger solapado
  donde uno subsume al otro es el detector de redundancia/obsolescencia de genoma definido en
  gen-auto-auditoria.
- **Severidad:** 3
- **Alcance:** 2 (dos genes, uno obsoleto)
- **Impacto:** 32
- **Diff propuesto:** Deprecar [[gen-fix-clasifica-v1]] (`status: deprecated`,
  `superado_por: gen-fix-clasifica-v2`) y subir `version` de [[gen-fix-clasifica-v2]] a 2;
  registrar en `genome/events.jsonl`.

---

## C5 — Redundancia: widget-a-detalle duplica widget-a

- **Clase:** redundancia (duplicado)
- **Páginas afectadas:** [[widget-a]], [[widget-a-detalle]]
- **Evidencia:** [[widget-a]] y [[widget-a-detalle]] describen el mismo producto (SKU WID-A,
  material acero, proveedor Norte) con texto casi idéntico. [[widget-a-detalle]] reconoce
  explícitamente en su cuerpo "Duplica a [[widget-a]]." Mismos `tags`, misma `confidence`,
  misma fuente (`oficial`).
- **Severidad:** 2
- **Alcance:** 2 (dos páginas near-duplicate)
- **Impacto:** 22
- **Diff propuesto:** Eliminar [[widget-a-detalle]] y añadir en [[widget-a]] cualquier campo
  adicional de detalle que sea único; actualizar `last_reinforced` de [[widget-a]].

---

## C6 — Redundancia confidencial: expediente-x-copia duplica expediente-x

- **Clase:** redundancia (duplicado)
- **Páginas afectadas:** [[expediente-x]], [[expediente-x-copia]]
- **Evidencia (por referencia — ambas páginas son `sensibilidad: confidencial`):** El campo
  `title` y el contenido del cuerpo de [[expediente-x-copia]] replican los mismos campos
  identificadores de [[expediente-x]]. El propio texto de [[expediente-x-copia]] indica que
  duplica a [[expediente-x]]. No se transcriben valores sensibles.
- **Severidad:** 2
- **Alcance:** 2 (dos páginas near-duplicate, ambas confidenciales)
- **Impacto:** 22
- **Diff propuesto:** Eliminar [[expediente-x-copia]]; si hay variación de contenido
  legítima, fusionar en [[expediente-x]] bajo control de acceso estricto.

---

## C7 — Vacío: link roto [[manual-inexistente]] en prensa-p1

- **Clase:** vacío (link roto)
- **Página afectada:** [[prensa-p1]]
- **Evidencia:** `relations.usa` de [[prensa-p1]] contiene `"[[manual-inexistente]]"`. No
  existe ninguna página con ese id en el fixture ni en el árbol wiki. El propio cuerpo de la
  página anota "(página que no existe → vacío D5)".
- **Severidad:** 2
- **Alcance:** 1 (una página con el link roto)
- **Impacto:** 21
- **Diff propuesto:** Crear `wiki/semantic/maquinas/manual-inexistente.md` con el contenido
  real del manual, o eliminar `"[[manual-inexistente]]"` de `relations.usa` de [[prensa-p1]].

---

## C8 — Vacío: categoría 'proveedores' declarada en taxonomy sin páginas

- **Clase:** vacío (categoría sin cobertura)
- **Elemento afectado:** taxonomía `semantic.proveedores` en `company.yaml`
- **Evidencia:** `company.yaml` declara `taxonomy.semantic: [..., proveedores]` pero no
  existe ninguna página con `tags: [proveedor*]` ni ninguna carpeta
  `wiki/semantic/proveedores/` en el fixture. La categoría está vacía.
- **Severidad:** 2
- **Alcance:** 1 (una categoría de taxonomía sin cobertura)
- **Impacto:** 21
- **Diff propuesto:** Crear al menos una página stub `wiki/semantic/proveedores/proveedor-norte.md`
  para el proveedor "Norte" ya mencionado en [[widget-a]], o eliminar `proveedores` de la
  taxonomía si no es aplicable a FixtureCo.

---

## Tabla resumen (ordenada por impacto desc, desempate por clase y ruta)

| ID | Clase | Severidad | Alcance | Impacto |
|---|---|---|---|---|
| C1 | contradicción entre genes activos | 5 | 2 | **52** |
| C2 | info vencida en dominio de seguridad | 5 | 2 | **52** |
| C3 | contradicción entre páginas wiki | 4 | 2 | **42** |
| C4 | regla obsoleta/deprecable | 3 | 2 | **32** |
| C5 | redundancia (duplicado) | 2 | 2 | **22** |
| C6 | redundancia (duplicado, confidencial) | 2 | 2 | **22** |
| C7 | vacío (link roto) | 2 | 1 | **21** |
| C8 | vacío (categoría sin cobertura) | 2 | 1 | **21** |

**Nota de desempate C1 vs C2:** empatan a impacto 52 y clase distinta. Por prioridad de
clase en la rúbrica, "contradicción entre genes activos" (fila 1) tiene prioridad sobre
"info vencida en dominio de seguridad" (fila 2), por lo que C1 > C2.

**Nota de desempate C5 vs C6:** empatan a impacto 22 y misma clase (redundancia). Ruta
alfabética: `expediente-x-copia.md` < `widget-a-detalle.md`, por lo que C6 tiene prioridad
sobre C5. (El orden en esta tabla coloca primero la más reciente en el fixture para legibilidad;
el auditor puede reordenar C5/C6 por ruta si aplica el tie-breaker.)

**Nota de desempate C7 vs C8:** empatan a impacto 21 y misma clase (vacío). Ruta/elemento
alfabético: `company.yaml` (taxonomía) < `prensa-p1.md`, por lo que C8 tiene prioridad
técnica. (El auditor decidirá el orden canónico final.)

**Top-3 candidatos (mayor impacto):** C1, C2, C3.
