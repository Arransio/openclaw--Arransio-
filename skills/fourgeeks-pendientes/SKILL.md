---
name: fourgeeks-pendientes
description: Lista las tareas PENDING de 4Geeks agrupadas por tipo. Usa para "¿qué me falta por entregar?" o "¿qué tengo pendiente?".
metadata: {"openclaw": {"requires": {"env": ["FOURGEEKS_TOKEN"]}}}
---

# Trabajo pendiente en 4Geeks

Muestra todo lo que queda por hacer, agrupado por tipo de tarea.

## Procedimiento

1. Lanza la llamada con `exec` (host `gateway`):

   ```bash
   curl -sS \
     -H "Authorization: Token $FOURGEEKS_TOKEN" \
     "https://breathecode.herokuapp.com/v1/assignment/user/me/task?limit=100&task_status=PENDING"
   ```

2. Agrupa las tareas por `task_type` (EXERCISE, PROJECT, LESSON, QUIZ) y ordénalas por tipo.

3. Presenta en el chat una lista numerada con tipo, título e id, más el desglose:

   ```
   ⏳ 51 pendientes: 35 EXERCISE · 9 PROJECT · 6 LESSON · 1 QUIZ

   1. [EXERCISE] n8n Glossary (id: …)
   2. [PROJECT] Network Troubleshooting Packet Tracer (id: …)
   ...
   ```

## Resultado esperado

El número de items listados debe coincidir con el `count` de la respuesta (referencia: 51). Si `count` es 0, informa de que no hay pendientes y termina. Los ids sirven para `entregar-tarea`.