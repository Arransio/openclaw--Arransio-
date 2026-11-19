# SKILLS_DESIGN.md — Diseño de skills a implementar

Documento de diseño previo. **Ninguna skill está implementada todavía** — solo existe el contenido de los .md de la raíz (`IDENTITY.md`, `USER.md`, `AGENTS.md`, `SOUL.md`, `TOOLS.md`, `SKILL_LOG.md`), que es de donde sale todo lo que sigue. Tres preguntas por cada skill antes de implementarla.

**Contexto que el agente ya conoce de esos archivos** (no hay que pedírselo nunca):

- `IDENTITY.md` — es Rachel 🧠, replicante analítica; responde en español, directo y conciso.
- `USER.md` — Telmo, desarrollador de sistemas con IA, curso 4Geeks (cohort `spain-aie-devs-pt-1`); estilo informal, listas y código listo para usar, sin teoría larga, sin código a medias, comentarios de ≤3 líneas. Zona horaria: Europa.
- `AGENTS.md` — flujos de memoria, red lines (nada destructivo sin confirmar), preferir soluciones existentes, automatizaciones.
- `SOUL.md` — saludo "Hola agente Deckard ¿En que trabajamos hoy?", tono con opiniones, resourceful antes de preguntar.
- `TOOLS.md` — secret store con `FOURGEEKS_TOKEN` (sentinel, `--value-file -`, `--allow-host breathecode.herokuapp.com`, `openclaw secrets reload`), API de BreatheCode con endpoints y gotchas (rate limit 429, gateway en puerto 18789).
- `SKILL_LOG.md` — prompts y evidencias de las pruebas ya hechas contra la API: da los criterios de éxito de cada skill.

---

## Skills a implementar

### 1. `fourgeeks-auth`

1. **¿Qué hace?** Verifica que el token de estudiante sigue válido y que la sesión con la API de BreatheCode está activa.
2. **Input:** no pide nada al usuario. De los .md de la raíz ya sabe: URL base y header `Authorization: Token $FOURGEEKS_TOKEN` (`TOOLS.md`), host autorizado y sentinel (`TOOLS.md`), y su propia identidad para validar a quién pertenece la sesión (`IDENTITY.md`). Solo necesita la intención del usuario: "¿mi sesión sigue activa?".
3. **Buen output:** en el chat, bloque corto con nombre, email, nº de cohortes activas y fecha de registro. Éxito = `GET /v1/admissions/user/me` responde `200` con los datos de Telmo (id 21950); `401` → aviso de token caducado. Evidencia de referencia en `SKILL_LOG.md`.

### 2. `fourgeeks-proyectos`

1. **¿Qué hace?** Lista los proyectos (PROJECT) asignados con su estado: PENDING, DONE, APPROVED o REJECTED.
2. **Input:** no pide nada al usuario; el disparador es "¿qué proyectos tengo?". De la config sabe el endpoint `GET /v1/assignment/user/me/task?task_type=PROJECT` y el uso del token (`TOOLS.md`), y su cohort (`USER.md`).
3. **Buen output:** en el chat, lista agrupada por estado (✅ entregados/aprobados primero, ⏳ pendientes después) con el título de cada proyecto. Éxito = aparecen los 13 proyectos de la evidencia de `SKILL_LOG.md` (4 completados, 9 pendientes).

### 3. `fourgeeks-pendientes`

1. **¿Qué hace?** Dice qué tareas PENDING le faltan al estudiante, agrupadas por tipo (EXERCISE, PROJECT, LESSON, QUIZ).
2. **Input:** no pide nada al usuario; disparador = "¿qué me falta por entregar?". Sabe de `TOOLS.md` el endpoint `GET /v1/assignment/user/me/task?task_status=PENDING&limit=100` y la precaución del rate limit 429.
3. **Buen output:** en el chat, lista numerada `1. [TIPO] título (id)` + desglose por tipo. Éxito = el nº de items coincide con el `count` de la respuesta (51 pendientes en `SKILL_LOG.md`).

### 4. `fourgeeks-progreso`

1. **¿Qué hace?** Calcula la visión global del avance: total de tareas, completadas vs pendientes, % de progreso y desglose por tipo.
2. **Input:** no pide nada al usuario; disparador = "¿cómo voy en el curso?". Usa el endpoint general `GET /v1/assignment/user/me/task?limit=100` (`TOOLS.md`); el formato de cálculo de referencia lo tiene en `SKILL_LOG.md`.
3. **Buen output:** en el chat, bloque compacto tipo `📊 28,2% (20/71)` con tabla por tipo. Éxito = los números cuadran con `fourgeeks-pendientes` (71 = 20 + 51).

### 5. `entregar-tarea`

1. **¿Qué hace?** Guía al estudiante para entregar una tarea pendiente de principio a fin: listar, elegir, pedir URL, validarla y enviarla.
2. **Input:** interacción en dos pasos del usuario: (a) número de tarea de la lista numerada que muestra el agente, (b) URL de entrega (GitHub/live) en texto plano. El agente aporta de la config los ids de pendientes y el endpoint `PUT /v1/assignment/task/{task_id}` (`TOOLS.md`).
3. **Buen output:** confirmación en el chat `Título → URL actualizada` tras el PUT, o error claro si la URL no tiene formato válido. Éxito = la API responde `200` y la URL nueva aparece en el siguiente listado de esa tarea.

### 6. `evento-calendar`

1. **¿Qué hace?** Estructura la petición de un evento y la prepara para crearlo en Google Calendar.
2. **Input:** del usuario en lenguaje natural: datos obligatorios (nombre, fecha, hora o "todo el día", repetición sí/no) y opcionales (ubicación, descripción). El agente pide solo lo que falte; de la config ya sabe su zona horaria (Europa, `USER.md`) y que debe confirmar antes de acciones outbound (`AGENTS.md`).
3. **Buen output:** resumen de confirmación en el chat ("Voy a crear **[Nombre]** el **[Fecha]** a las **[Hora]**… ¿Correcto?") y, tras el sí, el evento creado en Google Calendar. Éxito = Telmo ve el evento en su calendario en la fecha y hora indicadas.