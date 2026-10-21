---
name: entregar-tarea
description: "Listar tareas pendientes de 4Geeks, permitir seleccionar una, pedir una URL (GitHub/live) para actualizarla, validarla y enviarla. Responde a: Quiero entregar una tarea, actualizar URL de un proyecto, marcar entrega."
---

# Entregar una tarea en 4Geeks

## Descripción

Skill interactiva que guía al estudiante en la entrega de una tarea pendiente:

1. Muestra las tareas PENDING numeradas con su título y tipo.
2. Espera a que el usuario seleccione una (por número).
3. Pide la URL de entrega (GitHub, live, etc.).
4. Valida que la URL tenga un formato mínimo válido.
5. Envía la URL al endpoint de actualización.
6. Confirma el resultado o avisa si falla.

## Endpoints utilizados

- `GET /v1/assignment/user/me/task?task_status=PENDING&limit=100` — listar pendientes
- `PUT /v1/assignment/task/{task_id}` — actualizar URL de la tarea
- (Opcional) `POST /v1/assignment/task/{task_id}/deliver` — marcar como entregada

## Procedimiento

### Paso 1 — Obtener tareas pendientes

Ejecuta una llamada autenticada para listar las tareas PENDING.

- **Host:** `gateway`
- **URL:** `https://breathecode.herokuapp.com/v1/assignment/user/me/task?limit=100&task_status=PENDING`
- **Header:** `Authorization: Token ***`

Procesa el JSON. Si `count` es 0, informa que no hay tareas pendientes y termina.

### Paso 2 — Presentar lista numerada al usuario

Muestra las tareas pendientes en una lista numerada clara. Para cada tarea muestra:
- Número correlativo (1, 2, 3…)
- Título (`title`)
- Tipo (`task_type`: LESSON, EXERCISE, PROJECT, QUIZ)
- ID de la tarea (`id`) — mostrarlo entre paréntesis para referencia

Ejemplo:

```
Tareas pendientes:
1. [PROJECT] Mi primer portfolio (id: 12345)
2. [EXERCISE] Bucle for en Python (id: 12346)
3. [QUIZ] Variables y tipos (id: 12347)
```

### Paso 3 — Esperar selección

Pregunta al usuario qué tarea quiere entregar (por número). Espera su respuesta.

### Paso 4 — Pedir la URL

Una vez seleccionada la tarea, pide al usuario la URL de entrega. Indica claramente el formato esperado:

- **github_url**: URL del repositorio (ej: `https://github.com/usuario/repo`)
- **live_url**: URL del despliegue (ej: `https://midashboard.netlify.app`)

Pregunta si quiere actualizar `github_url`, `live_url` o ambas.

### Paso 5 — Validar la URL

Antes de enviar, valida que la URL tenga un formato mínimo:

- Debe comenzar con `http://` o `https://`
- Debe contener al menos un punto después del dominio o una ruta no vacía
- Longitud máxima razonable (ej: < 2048 caracteres)

Si la URL no es válida, informa al usuario y vuelve al paso 4. Si es válida, continúa.

### Paso 6 — Enviar la actualización

Ejecuta una llamada autenticada PUT al endpoint de la tarea.

- **Host:** `gateway`
- **URL:** `https://breathecode.herokuapp.com/v1/assignment/task/{task_id}`
- **Header:** `Authorization: Token ***`, `Content-Type: application/json`
- **Body:** `{"github_url": "https://...", "live_url": "https://..."}` (solo los campos que haya proporcionado el usuario)

### Paso 7 — Verificar resultado

- Si la respuesta es **200**, confirma al usuario que la URL se actualizó correctamente.
- Si la respuesta es un error, muestra el mensaje de error y sugiere revisar la URL o intentar de nuevo.

### Paso 8 — (Opcional) Preguntar si quiere marcar como entregada

Después de actualizar la URL, pregunta: *"¿Quieres marcar la tarea como entregada para revisión?"*

Si el usuario dice que sí, haz una llamada POST a `/v1/assignment/task/{task_id}/deliver`.

- **Host:** `gateway`
- **URL:** `https://breathecode.herokuapp.com/v1/assignment/task/{task_id}/deliver`
- **Header:** `Authorization: Token ***`

Informa del resultado.

## Validación de URL

Usa una comprobación básica mediante `curl` o expresión regular:

```bash
# Validación mínima con grep
echo "$url" | grep -qE '^https?://[a-zA-Z0-9]([a-zA-Z0-9.-]*[a-zA-Z0-9])?(:[0-9]+)?(/.*)?$'
```

Si el entorno no tiene `grep`, usa una verificación con `curl --output /dev/null --silent --head --write-out '%{http_code}' "$url"` para comprobar que la URL responde (opcional, depende de si la URL es pública).

## Manejo de errores

| Situación | Acción |
|---|---|
| No hay tareas pendientes | Informar y terminar |
| El usuario elige un número inválido | Pedir que seleccione de nuevo |
| URL mal formada | Decir por qué y pedirla otra vez |
| PUT responde 400/404/500 | Mostrar error y sugerir reintentar |
| Token expirado (401) | Remitir a fourgeeks-token-refresh |

## Resultado esperado

El estudiante puede actualizar la URL de entrega de una tarea pendiente de forma guiada, con validación previa y confirmación del resultado, sin necesidad de tocar la API directamente.