---
name: fourgeeks-proyectos
description: "Listar los proyectos (PROJECT) asignados en 4Geeks con su estado (PENDING/DONE/APPROVED/REJECTED). Responde a: ¿Qué proyectos tengo? ¿Cuáles están entregados? ¿Cuáles aprobados?"
---

# Obtener proyectos de 4Geeks

## Descripción

Skill para recuperar la lista de proyectos (tareas de tipo PROJECT) asignados al estudiante con su estado actual: pendiente, completado, aprobado o rechazado.

## Endpoint utilizado

`GET /v1/assignment/user/me/task` — devuelve tareas asignadas al usuario autenticado, filtrables por `task_type`, `task_status` y `cohort`.

## Procedimiento

1. Ejecuta una llamada autenticada al endpoint de tareas con filtro de tipo `PROJECT`.
   - Host: `gateway`
   - URL: `https://breathecode.herokuapp.com/v1/assignment/user/me/task?limit=100&task_type=PROJECT`
   - Header: `Authorization: Token $FOURGEEKS_TOKEN`
   - (Opcional) Si se especifica un cohorte, añade `&cohort={cohort_id}`.

2. Procesa la respuesta JSON. De cada proyecto extrae:
   - Título (`title`)
   - Estado (`task_status`: PENDING, DONE, APPROVED, REJECTED)
   - ID de la tarea
   - URLs de entrega si existen (`github_url`, `live_url`)

3. Presenta los resultados agrupados por estado, con un total general.

## Resultado esperado

Una lista clara de proyectos con su estado actual, que permite al estudiante saber de un vistazo qué proyectos tiene pendientes, cuáles ha entregado y cuáles han sido evaluados.