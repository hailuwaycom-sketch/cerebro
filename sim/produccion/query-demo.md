# QUERY demo — trazabilidad de lote y calidad

> Operación `QUERY`: navega desde [[index.md]] por relaciones, abre solo lo necesario,
> cita páginas-fuente con su `confidence`, advierte sobre decaimiento.

## Pregunta (caso real de planta)
**"El cliente AutoEnsamble Andina reclama una pieza SM45 con la altura fuera de medida.
¿De qué lote salió, en qué máquina se produjo, hubo no-conformidad y se corrigió la causa?"**

## Navegación (presupuesto de contexto)
1. `index.md` → producto [[soporte-motor-SM45]].
2. SM45 → `fabricado_como` → [[LOTE-SM45-2606]].
3. Lote → `bajo_orden` [[OP-2026-0417]], `producido_en` [[PR-200-prensa]],
   `afectado_por` [[NCR-2026-031]].
4. NCR → `causa_en` [[PR-200-prensa]], `mitiga_con` [[MTTO-PR-200-2026-06]],
   `viola` [[SOP-CAL-03-inspeccion-dimensional]].

(No se leyó ninguna otra página; 6 páginas abiertas de 13.)

## Respuesta
La pieza pertenece al **LOTE-SM45-2606** (1.200 uds), producido bajo la orden
**OP-2026-0417** en la prensa **PR-200** de la línea L1-estampado, con acero CRS-1.5mm de
**AceroNorte**. Sí hubo no-conformidad: **NCR-2026-031** (severidad **mayor**), 200 piezas
con altura 45,35–45,5 mm (nominal 45,0 ±0,2; Cpk 0,71). **Causa raíz:** desgaste de troquel
y corrimiento del tope de altura en PR-200, agravado por incumplir el paso de verificación
de primera pieza del **SOP-CAL-03**. **Disposición:** reproceso de las 200 piezas; el resto
del lote conforme. **Corrección:** intervención **MTTO-PR-200-2026-06** (2026-06-19)
recalibró el tope; PR-200 quedó **operativa**. Conformidad final del lote verificada
2026-06-20.

### Citas
- [[LOTE-SM45-2606]] (confidence 0.9)
- [[OP-2026-0417]] (0.95)
- [[PR-200-prensa]] (0.95) — estado actual: operativa
- [[NCR-2026-031]] (0.9) · **aviso de decaimiento:** `decay_rate: high`, evento del
  2026-06-19; reverificar antes de afirmar a un cliente nuevo.
- [[MTTO-PR-200-2026-06]] (0.9, decay high)
- [[SOP-CAL-03-inspeccion-dimensional]] (0.95)

### Observación de fricción (alimenta findings)
La cadena `producido_en / bajo_orden / afecta_a` no es vocabulario del genoma base
(`relations` solo define `usa, depende_de, contradice, reemplaza`). Funcionó porque el
gen del sector lo introdujo, pero el genoma base no lo soporta nativamente.
