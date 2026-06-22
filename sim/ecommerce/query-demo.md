# QUERY demo — TiendaNube Andina (sandbox e-commerce)

Demuestra navegación del grafo desde `wiki/index.md` por relaciones, citando páginas-fuente
y su `confidence`, respetando `gen-query` (advierte datos vencidos por `decay_rate`) y los
genes del sector.

---

## Consulta
> "¿Puedo comprar ya la Cafetera Aurora plateada y a qué precio? ¿Hay algún problema
> recurrente con ese modelo que deba saber antes de surtir más?"

## Navegación (presupuesto de contexto)
`index.md` → [[SKU-CAF-2200]] → relación `referido_por` → [[sintesis-portafiltro-aurora]]
→ `agrega` tickets + `refuerza` [[resenas-aurora]]; rama `depende_de` → [[prov-electrohogar-import]].
4 páginas abiertas, no toda la wiki.

## Respuesta
**Precio y disponibilidad — con advertencia de frescura.** La Cafetera Aurora plateada
(**SKU-CAF-2200**) figura a **$389.900** con **42 u en Bogotá / 11 en Medellín**, pero ese
dato es **`valido_a: 2026-06-20`** y hoy es 2026-06-22: por [[gen-dato-volatil]] el precio y
el stock son `decay_rate: high`, así que **no se afirman como vigentes** — hay que reconsultar
el PIM antes de cerrar venta o reorden. Los **specs estables** (15 bar, 1.2 L, acero inox)
sí son confiables (`confidence 0.95`, `decay_rate: low`).
Fuente: [[SKU-CAF-2200]] ← `raw/catalogo-2026-06.md`.

**Problema recurrente — SÍ, defecto sistémico de portafiltro.** Hay una síntesis activa
([[sintesis-portafiltro-aurora]], `confidence 0.9`) que agrega **4 tickets** (TKT-5521, 5530,
5544, 5561) y **2 reseñas 2/5** ([[resenas-aurora]], señal blanda `confidence 0.4`). El
defecto aparece en la variante plata **y** en la negra ([[SKU-CAF-2201]]), por lo que apunta
a la **familia / lote del proveedor [[prov-electrohogar-import]]** (notificado 2026-06-15, un
ticket aún abierto), no al color.
**Recomendación citada:** antes de surtir más unidades, esperar confirmación de compras sobre
el lote del proveedor — riesgo de RMA en cadena.
