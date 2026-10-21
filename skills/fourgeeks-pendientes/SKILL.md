---
name: fourgeeks-pendientes
description: "Decir qué tareas le faltan por completar al estudiante en 4Geeks (PENDING). Responde a: ¿Qué me falta por entregar? ¿Qué ejercicios tengo pendientes?"
---

# Obtener trabajo pendiente en 4Geeks

## Descripción

Skill para identificar específicamente qué tareas están pendientes de completar (PENDING) en la cuenta de 4Geeks del estudiante, desglosadas por tipo.

## Endpoint utilizado

`GET /v1/assignment/user/me/task` — devuelve tareas asignadas al usuario autenticado, filtrables por `task_status`.

## Procedimiento

1. Ejecuta una llamada autenticada al endpoint de tareas con filtro `PENDING`.
   - Host: `gateway`
   - URL: `https://breathecode.herokuapp.com/v1/assignment/user/me/task?limit=100&task_status=PENDING`
   - Header: `Authorization: Token $FOURGEEKS_TOKEN`

2. Procesa la respuesta JSON. Agrupa las tareas pendientes por tipo (`task_type`: LESSON, EXERCISE, PROJECT, QUIZ).

3. Para cada tarea pendiente muestra:
   - Título
   - Tipo
   - Slug asociado

4. Presenta un resumen con:
   - Total de tareas pendientes
   - Desglose por tipo (X ejercicios, Y proyectos, Z lecciones, W quizzes)

## Resultado esperado

Una visión clara y accionable de todo lo que queda por hacer, ordenada por tipo de tarea, para que el estudiante pueda priorizar su trabajo.