# sim/ — banco de pruebas de CEREBRO

Escenarios simulados de distintas industrias para **probar y afinar el genoma base**.
Cada carpeta es un sandbox aislado: NO toca el cerebro real (`D:/cerebro/genome`, `wiki`).

## Estructura por escenario
```
sim/<industria>/
├── company.yaml      # manifiesto de ONBOARD (entrada reproducible)
├── raw/              # 2-3 fuentes de muestra realistas
├── genome-applied/   # genes sembrados por ONBOARD a partir del manifiesto
├── wiki/             # páginas generadas por INGEST (con frontmatter + relaciones)
├── query-demo.md     # una consulta QUERY y su respuesta con citas
└── findings.md       # fricciones detectadas + propuestas de mutación al genoma base
```

## Para qué sirve
Correr el **full loop** (onboard → ingest → query → evolve) en industrias con taxonomías
muy distintas revela dónde el genoma base se queda corto. Cada fricción se convierte en una
**propuesta de mutación** a los genes base de `D:/cerebro/genome/`, que se aplica al cerebro
real solo tras aprobación (modo híbrido, [[gen-compuerta-mutacion]]).

## Escenarios
- `produccion/` — industria de producción / manufactura
- `agencia/` — agencia de automatización / marketing
- `legal/` — bufete legal
- `salud/` — clínica / salud
- `ecommerce/` — e-commerce / retail
