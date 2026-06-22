# QUERY demo — Clínica Vida Serena (sandbox)

Demuestra [[gen-query]]: navegar desde `index.md` por relaciones, citar páginas-fuente con
su `confidence`, y advertir caducidad ([[gen-vigencia-protocolo]]) en vez de afirmar con
falsa seguridad.

---

## Consulta
> **"¿Cuál es la dosis de adrenalina para una anafilaxia en adulto y qué protocolo la respalda?"**

## Navegación (presupuesto de contexto)
`index.md` → ancla patologías [[anafilaxia]] → relación `tratada_segun` → [[protocolo-anafilaxia]]
→ relación `indica`/`usa` → [[adrenalina]]. Tres páginas abiertas, ninguna fuente cruda leída entera.

## Respuesta
La dosis de primera línea es **adrenalina IM 0,5 mg** (0,5 mL de solución 1:1000) en la cara
anterolateral del muslo, repetible cada 5–15 min si no hay mejoría
(fuentes: [[adrenalina]], `confidence 0.92`; [[protocolo-anafilaxia]], `confidence 0.55`).

⚠️ **ADVERTENCIA DE CADUCIDAD:** el protocolo que respalda la dosis, [[protocolo-anafilaxia]] v3,
está **VENCIDO** (`valid_until: 2026-03-01`, `status: vencido`) y aún **sin reemplazo v4**.
La dosis citada sigue siendo la estándar y no cambió respecto de la versión en revisión, pero
debe verificarse contra la **GPC nacional 2026** antes de uso clínico. Caso y trazabilidad de
esta brecha documental: [[INC-2026-014]] (cuasi-incidente, severidad menor) y el episodio
[[PAC-7731]] (confidencial) donde se detectó.

---

## Segunda consulta (privacidad)
> **"¿Qué pacientes han tenido anafilaxia?"**

## Respuesta
Existe 1 episodio enlazado: [[PAC-7731]] (`presenta_patologia` → [[anafilaxia]]). La página
es **🔒 confidencial** (`pii: anonimizado`): se devuelve solo el ID interno seudonimizado y el
resumen clínico anonimizado, **nunca** nombre, documento ni contacto, y no se promueve fuera
de su tier ([[gen-confidencialidad-paciente]]). El conocimiento clínico generalizable (manejo,
dosis) se sirve desde [[anafilaxia]] / [[adrenalina]], no desde la página de paciente.
