---
name: fourgeeks-progreso
description: "Dar una visión general del progreso en 4Geeks: total de tareas, completadas vs pendientes, porcentaje de avance. Responde a: ¿Qué tal voy en el curso? ¿Cuánto llevo avanzado?"
---

# Resumen de progreso en 4Geeks

## Descripción

Skill para obtener una visión general del avance en el curso: cuántas tareas están completadas frente a pendientes, desglose por tipo, y métrica de progreso global.

## Endpoint utilizado

`GET /v1/assignment/user/me/task` — devuelve todas las tareas asignadas al usuario autenticado.

## Procedimiento

1. Ejecuta una llamada autenticada al endpoint de tareas sin filtros de estado para obtener el conjunto completo.
   - Host: `gateway`
   - URL: `https://breathecode.herokuapp.com/v1/assignment/user/me/task?limit=100`
   - Header: `Authorization: Token $FOURGEEKS_TOKEN`

2. Procesa la respuesta JSON completa. Calcula:

   a. **Totales generales:**
      - Total de tareas asignadas
      - Total completadas (DONE + APPROVED)
      - Total pendientes (PENDING)
      - Total rechazadas (REJECTED)

   b. **Desglose por tipo:**
      - Proyectos: total, completados, pendientes
      - Ejercicios: total, completados, pendientes
      - Lecciones: total, completadas, pendientes
      - Quizzes: total, completados, pendientes

   c. **Porcentaje de progreso:**
      - % global = (completadas / total) × 100

3. Presenta la información de forma visual y resumida para una lectura rápida.

## Resultado esperado

Una visión general del progreso en el curso que permite al estudiante saber de un vistazo cuánto ha avanzado y qué áreas tienen más trabajo pendiente.