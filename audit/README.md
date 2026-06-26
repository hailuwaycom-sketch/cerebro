# audit/ — estado de corridas de auto-auditoría

Salida de la operación `AUDIT` (regla: [[gen-auto-auditoria]]). Estado operacional, NO genoma
ni conocimiento. Una carpeta por corrida:

```
audit/runs/<YYYY-MM-DD>-<short-SHA>/
├── 00-snapshot.md   # SHA, arbol limpio/sucio, archivos auditados, version del gen, fecha
├── 10-maker.md      # todos los candidatos: evidencia + diff + score
├── 20-auditor.md    # veredicto por candidato + re-derivacion + score recalculado
└── 30-proposals.md  # <=3 propuestas rankeadas, status: pending|approved|reverted
```

Claveado al SHA de git: misma base ⇒ misma corrida reconstruible. Las decisiones humanas se
anotan en `30-proposals.md`. Las mejoras aprobadas que tocan genoma pasan por
[[gen-compuerta-mutacion]] (`events.jsonl`); las de wiki, por `log.md`.
