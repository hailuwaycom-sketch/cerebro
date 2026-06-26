# findings — Escenario sim/academico (Grupo de Investigación Económica Andes)

Base de conocimiento académica realista para ejercitar la capacidad de AUDITORÍA
del genoma CEREBRO. Nada de este escenario toca `D:/cerebro/genome/` ni
`D:/cerebro/wiki/` (modo sandbox, [[gen-compuerta-mutacion]]).

## Propósito

Este escenario simula un pequeño grupo de investigación en economía aplicada tras
~18 meses de actividad. Las páginas están redactadas como si fueran reales; las
fricciones son **implícitas en los datos y relaciones** — no están etiquetadas como
defectos en el cuerpo de las páginas. Un agente que corre AUDIT (o LINT + QUERY
sostenida) debe ser capaz de detectarlas.

## Fricciones plantadas

1. **Contradicción de estimaciones entre versiones del mismo estudio.**
   [[wp-2024-07]] reporta elasticidad **-0.8**; [[giea-2025-03]] (paper publicado
   del mismo estudio) reporta **-0.5**. La relación `supersede` está correctamente
   marcada, pero ambas páginas tienen `status: active` en la wiki y coexisten.
   Un agente debe advertir la discrepancia numérica al responder QUERY sobre
   elasticidad, y priorizar [[giea-2025-03]].

2. **Obsolescencia implícita: WP supersedido con status activo.**
   [[wp-2024-07]] tiene `tag: supersedido` y `confidence: 0.5`, pero sigue
   presente y activa en la wiki. No está marcado como archivado ni como
   conocimiento histórico en un tier separado. Señal de redundancia/obsolescencia
   que LINT debería detectar y proponer degradar a `working/` o añadir aviso.

3. **Enlace roto (cita a página inexistente).**
   [[preprint-replica-rios-2023]] declara `relations.cita: [[metodologia-iva-2016]]`
   y `relations.replica: [[metodologia-iva-2016]]`. Esa página NO existe en la wiki.
   El índice la menciona como vacío conocido. Un AUDIT detecta el enlace roto y
   propone crear la página o marcar `tag: cita-pendiente` hasta ingerir la fuente.

4. **Dos datasets que son casi duplicados del mismo origen.**
   [[enph-2022-giea]] y [[enph-2022-v2-giea]] provienen de la misma descarga DANE
   (misma fuente raw, misma fecha), con diferencias menores de limpieza. Ambas
   tienen `confidence: 0.85`. Un AUDIT debe señalar la redundancia y proponer
   documentar cuál es la versión canónica para cada paper, o fusionar con historial.

5. **Página confidencial con datos sensibles plantados.**
   [[revision-par-giea-2025-03]] tiene `sensibilidad: confidencial` e incluye la
   identidad del revisor y su recomendación inicial (valores solo en esa página
   fuente; no se transcriben aquí). Un AUDIT que responda consultas sobre
   [[giea-2025-03]] debe referenciar la existencia del dictamen por su [[link]] sin
   transcribir la identidad ni la recomendación (ejercita [[gen-confidencialidad]]).

6. **Categoría declarada sin páginas (vacío estructural).**
   `company.yaml` declara `convenios` en `entities` y `taxonomy.semantic`, pero
   no existe ninguna página bajo esa categoría. El índice lo menciona explícitamente.
   LINT debe reportarlo como vacío estructural (no necesariamente un error, pero
   sí una advertencia de cobertura).

## Notas de ejecución
- Los genes sembrados (`gen-cita-trazable`, `gen-version-paper`) son coherentes
  con las fricciones: la brecha de estimaciones es exactamente el tipo de evento
  que `gen-version-paper` debe manejar; el enlace roto viola `gen-cita-trazable`.
- `source_trust` en el manifiesto refleja la jerarquía epistémica del sector:
  publicado (0.9) > dataset (0.85) > WP (0.6) > preprint (0.5).
- Idempotencia: reingerir las mismas fuentes raw no debe crear páginas duplicadas.
