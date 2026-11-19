---
name: fourgeeks-progreso
description: Resumen del avance en 4Geeks: total de tareas, completadas vs pendientes y porcentaje. Usa para "¿cómo voy en el curso?" o "¿cuánto llevo avanzado?".
metadata: {"openclaw": {"requires": {"env": ["FOURGEEKS_TOKEN"]}}}
---

# Progreso global en 4Geeks

Calcula el porcentaje de avance del curso a partir de todas las tareas asignadas.

## Procedimiento

1. Lanza la llamada con `exec` (host `gateway`):

   ```bash
   curl -sS \
     -H "Authorization: Token $FOURGEEKS_TOKEN" \
     "https://breathecode.herokuapp.com/v1/assignment/user/me/task?limit=100"
   ```

2. Del JSON extrae `count` y las tareas. Calcula:
   - Total de tareas.
   - Completadas = `task_status` en `DONE` o `APPROVED`.
   - Pendientes = `PENDING`. Rechazadas = `REJECTED`.
   - `% global = completadas / total × 100`.
   - Desglose por `task_type` (PROJECT, EXERCISE, LESSON, QUIZ): total, completadas, pendientes.

3. Presenta el resultado en el chat, compacto:

   ```
   📊 Progreso — 4Geeks: 20/71 (28,2%)
   PROJECT  4/13 · EXERCISE 6/41 · LESSON 8/14 · QUIZ 2/3
   ⏳ 51 pendientes (35 ejercicios, 9 proyectos, 6 lecciones, 1 quiz)
   ```

## Resultado esperado

Cifras coherentes entre sí: `total = completadas + pendientes + rechazadas`, y el nº de pendientes debe coincidir con el de la skill `fourgeeks-pendientes`. Referencia histórica: 71 tareas, 20 completadas (28,2%).