---
name: fourgeeks-proyectos
description: Lista los proyectos de 4Geeks con su estado (PENDING/DONE/APPROVED/REJECTED). Usa para "¿qué proyectos tengo?", "¿cuáles están entregados o aprobados?".
metadata: {"openclaw": {"requires": {"env": ["FOURGEEKS_TOKEN"]}}}
---

# Proyectos de 4Geeks

Recupera los proyectos asignados al estudiante con su estado actual.

## Procedimiento

1. Lanza la llamada con `exec` (host `gateway`):

   ```bash
   curl -sS \
     -H "Authorization: Token $FOURGEEKS_TOKEN" \
     "https://breathecode.herokuapp.com/v1/assignment/user/me/task?limit=100&task_type=PROJECT"
   ```

2. De cada proyecto extrae `title`, `task_status`, `id` y, si existen, `github_url` y `live_url`.

3. Presenta en el chat agrupado por estado:

   ```
   ✅ Aprobados/entregados (4)
   - Milestone — Choose Your Company
   ...
   ⏳ Pendientes (9)
   1. Network Troubleshooting Packet Tracer (id: …)
   ...
   ```

## Resultado esperado

Una lista clara de los 13 proyectos con su estado (referencia: 4 completados, 9 pendientes). Los aprobados primero; los pendientes numerados para poder referenciarlos desde `entregar-tarea`.