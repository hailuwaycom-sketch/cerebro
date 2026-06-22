# Registro de Incidente de Seguridad — INC-2026-014

**Fecha de detección:** 2026-06-21
**Reportado por:** Enf. Rivas (Enfermería) — vía sistema de notificación de EA
**Tipo:** Cuasi-incidente (near miss) — sin daño al paciente
**Episodio relacionado:** EP-2026-0488 (paciente PAC-7731)
**Servicio:** Urgencias menores

## Descripción
Durante la atención de una anafilaxia (episodio EP-2026-0488), el equipo consultó el
protocolo institucional PROT-URG-ANAF-003 v3, cuya **vigencia caducó el 2026-03-01**. La
revisión bienal que debía producir la v4 no se publicó a tiempo. El manejo fue correcto
porque la dosis de adrenalina IM no cambió, pero se actuó sobre un documento vencido.

## Severidad
**Menor** — sin daño; el riesgo es de gobernanza documental, no clínico en este caso.

## Causa raíz
No existe alerta automática de caducidad de protocolos. La vigencia se controla de forma
manual y se pasó por alto la fecha límite de revisión bienal.

## Acciones correctivas
1. Marcar PROT-URG-ANAF-003 v3 como VENCIDO en el repositorio hasta publicar v4.
2. Acelerar publicación de la v4 alineada a la GPC nacional 2026.
3. Solicitar a Sistemas un mecanismo de alerta de `valid_until` para todo protocolo.
4. Auditar otros protocolos con revisión vencida (dolor torácico, sepsis).

## Estado
Abierto — pendiente publicación de v4 y alerta automatizada.
