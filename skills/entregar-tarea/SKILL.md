---
name: entregar-tarea
description: Entrega una tarea pendiente de 4Geeks de forma guiada: elegir tarea, validar URL y enviarla. Usa para "quiero entregar una tarea" o "actualizar URL de un proyecto".
metadata: {"openclaw": {"requires": {"env": ["FOURGEEKS_TOKEN"]}}}
---

# Entregar una tarea en 4Geeks

Flujo interactivo para actualizar la URL de entrega de una tarea pendiente.

## Procedimiento

### 1. Listar pendientes

Lanza con `exec` (host `gateway`):

```bash
curl -sS \
  -H "Authorization: Token $FOURGEEKS_TOKEN" \
  "https://breathecode.herokuapp.com/v1/assignment/user/me/task?limit=100&task_status=PENDING"
```

Si `count` es 0, informa y termina. Presenta la lista numerada con tipo, título e id:

```
1. [PROJECT] Mi primer portfolio (id: 12345)
2. [EXERCISE] Bucle for en Python (id: 12346)
```

### 2. Pedir la selección

Espera a que el usuario elija un número. Si el número no está en la lista, pide que seleccione de nuevo.

### 3. Pedir la URL

Pregunta qué quiere actualizar: `github_url` (ej. `https://github.com/usuario/repo`), `live_url` (ej. `https://midashboard.netlify.app`) o ambas, y pide la URL en texto plano.

### 4. Validar la URL

Antes de enviar, valida el formato:

- Empieza por `http://` o `https://`.
- Dominio con al menos un punto y ruta no vacía.
- Menos de 2048 caracteres.

Si no es válida, explica por qué y vuelve al paso 3.

### 5. Enviar

```bash
curl -sS -X PUT \
  -H "Authorization: Token $FOURGEEKS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"github_url": "...", "live_url": "..."}' \
  "https://breathecode.herokuapp.com/v1/assignment/task/{task_id}"
```

Incluye solo los campos proporcionados por el usuario.

### 6. Verificar

- **200** → confirma en el chat: `Título → URL actualizada`.
- Error 400/404/500 → muestra el mensaje y sugiere revisar la URL.
- **401** → token caducado; remite a `fourgeeks-auth`.

### 7. (Opcional) Marcar como entregada

Pregunta: *"¿Quieres marcarla como entregada para revisión?"*. Si dice que sí:

```bash
curl -sS -X POST \
  -H "Authorization: Token $FOURGEEKS_TOKEN" \
  "https://breathecode.herokuapp.com/v1/assignment/task/{task_id}/deliver"
```

## Resultado esperado

La tarea queda con la URL nueva (visible en el siguiente listado) y, si el usuario lo pide, marcada como entregada. Todo guiado en el chat, sin que el usuario toque la API.

## Manejo de errores

| Situación | Acción |
|---|---|
| Sin tareas pendientes | Informar y terminar |
| Número inválido | Pedir selección otra vez |
| URL mal formada | Explicar el motivo y repetir paso 3 |
| PUT con error | Mostrar el error y sugerir reintento |
| 401 | Remitir a `fourgeeks-auth` |