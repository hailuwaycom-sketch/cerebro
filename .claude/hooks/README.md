# Hooks de CEREBRO — del stub al loop real

Hoy los 3 hooks de `../settings.json` son **stubs** que solo imprimen un recordatorio.
Eso mantiene el sistema inerte y seguro hasta que decidas activarlos. Para cerrar el
loop de memoria infinita de verdad, reemplaza cada `command` por un script que actúe.

## Qué debe hacer cada hook
- **SessionStart** → inyectar al contexto el contenido de `index.md`, la cola de `log.md`
  y el índice de genes activos. (Puede ser un script que imprima esos archivos a stdout;
  Claude Code añade la salida del hook al contexto inicial.)
- **PreCompact** → antes de que el contexto se compacte, escribir en `wiki/working/` una
  nota con frontmatter que capture lo valioso de la sesión en curso. Este es el corazón
  de la "memoria infinita": lo que se volcó a disco sobrevive a la compactación.
- **Stop** → al cerrar la sesión, generar el resumen episódico en `wiki/episodic/` y
  correr `EVOLVE` en **modo propuesta** (detecta patrones, no aplica nada solo).

## Recomendación de implementación
1. Empieza por `SessionStart` (el más simple y de mayor valor inmediato).
2. Crea los scripts en esta carpeta (ej. `session-start.ps1`, `precompact.ps1`, `stop.ps1`)
   y apunta los `command` de `settings.json` a ellos.
3. Versiónalos en git como parte del genoma operativo.

> Nota Windows: los `command` corren en shell. Usa rutas relativas a la raíz del repo.
