# 📋 SKILL_LOG.md — Mi Asistente 4Geeks

> Proyecto: Enseñándole a OpenClaw a seguir tu progreso en 4Geeks Academy  
> Estudiante: Telmo Arranz Villalva (atdedits@gmail.com)  
> Cohort: spain-aie-devs-pt-1 (id: 1838)  
> Fecha: 2026-10-13

---

## Configuración inicial

### Token de estudiante

El token `FOURGEEKS_TOKEN` se almacenó de forma segura en el secret store de OpenClaw mediante:

```bash
openclaw secrets store set FOURGEEKS_TOKEN --kind secret
```

Se vinculó al host permitido para el egress proxy:

```bash
openclaw secrets store set FOURGEEKS_TOKEN --allow-host breathecode.herokuapp.com
```

Y se activó el proxy de egress para que los comandos del agente puedan usar el token sin exponer su valor:

```bash
openclaw config set secrets.egressProxy.enabled true --strict-json
openclaw gateway restart
```

> El token se usa mediante un *sentinel* opaco (`$FOURGEEKS_TOKEN`) que el Gateway sustituye por el valor real solo al salir hacia el host autorizado. El agente nunca ve el valor plano del token.

---

## Skill 1 — Autenticar

### Prompt inicial

> "Quiero que puedas conectarte a mi cuenta de 4Geeks usando mi token de estudiante, sin que tenga que desarrollar código de mi parte. ¿Qué debemos hacer?"

### Lo que hace

Verifica que el token de estudiante es válido y que la sesión con la API de BreatheCode está activa. Consulta el endpoint `/v1/admissions/user/me` y devuelve los datos básicos del estudiante (nombre, email, cohortes activas).

### Endpoint(s) utilizado(s)

| Método | Ruta | Auth |
|--------|------|------|
| GET | `/v1/admissions/user/me` | Token requerido |

### Evidencia de funcionamiento

```
GET https://breathecode.herokuapp.com/v1/admissions/user/me → HTTP 200
✅ Autenticación OK
   Usuario: Telmo Arranz Villalva
   Email: atdedits@gmail.com
   ID: 21950
   Cohortes activas: spain-aie-devs-pt-1, AI Engineering Introduction, Introduction to Cybersecurity
```

---

## Skill 2 — Obtener mis proyectos

### Prompt inicial

> "Ahora quiero que puedas decirme qué proyectos tengo asignados y en qué estado están."

### Lo que hace

Recupera la lista de tareas de tipo **PROJECT** asignadas al estudiante con su estado actual: PENDING, DONE, APPROVED o REJECTED.

### Endpoint(s) utilizado(s)

| Método | Ruta | Auth |
|--------|------|------|
| GET | `/v1/assignment/user/me/task?task_type=PROJECT` | Token requerido |

### Evidencia de funcionamiento

```
GET https://.../v1/assignment/user/me/task?limit=100&task_type=PROJECT → HTTP 200
Total proyectos: 13
  PENDING: 9
  DONE: 4

Proyectos pendientes:
  - Network Troubleshooting Packet Tracer
  - Milestone — AI-driven Engineering
  - Creating a NAT network on VirtualBox
  - Milestone — Final Project Video: 5-Minute AI Pitch
  - Setting Up Your Personal AI Agent with OpenClaw
  - My Agent, My Way: Teaching Your Personal Assistant New Skills
  - Understanding Networks Internals
  - Milestone 0 — Choose Your Company
  - My 4Geeks Assistant — Teaching OpenClaw to Track Your Progress

Proyectos completados:
  - ✅ Milestone — Choose Your Company
  - ✅ Building context from an existing project - Financial dashboard
  - ✅ Operations Backoffice – Incident Manager
  - ✅ Operations Backoffice – Inventory Manager
```

---

## Skill 3 — Obtener trabajo pendiente

### Prompt inicial

> "¿Qué me falta por entregar en el curso?"

### Lo que hace

Identifica específicamente qué tareas están pendientes (PENDING) y las agrupa por tipo (EXERCISE, PROJECT, LESSON, QUIZ) para dar una visión clara y accionable de lo que queda por hacer.

### Endpoint(s) utilizado(s)

| Método | Ruta | Auth |
|--------|------|------|
| GET | `/v1/assignment/user/me/task?task_status=PENDING` | Token requerido |

### Evidencia de funcionamiento

```
GET https://.../v1/assignment/user/me/task?limit=100&task_status=PENDING → HTTP 200
Total pendientes: 51

Desglose por tipo:
  EXERCISE: 35
  PROJECT:  9
  LESSON:   6
  QUIZ:     1

Ejercicios pendientes: n8n Glossary, AI Context Engineering..., AI Coding Rules..., etc.
Proyectos pendientes: Network Troubleshooting Packet Tracer, Milestone - AI-driven Engineering, etc.
Lecciones pendientes: Introduction To No Code And Automatization, ...
Quizzes pendientes: Quiz Ip Adresses Protocols
```

---

## Skill 4 — Obtener resumen de progreso

### Prompt inicial

> "¿Qué tal voy en el curso? Dame una visión general de mi avance."

### Lo que hace

Procesa todas las tareas asignadas y calcula métricas de progreso global: total de tareas, completadas vs pendientes, porcentaje de avance, y desglose por tipo de tarea.

### Endpoint(s) utilizado(s)

| Método | Ruta | Auth |
|--------|------|------|
| GET | `/v1/assignment/user/me/task` | Token requerido |

### Evidencia de funcionamiento

```
GET https://.../v1/assignment/user/me/task?limit=100 → HTTP 200

📊 RESUMEN DE PROGRESO — 4Geeks
   Total tareas: 71
   ✅ Completadas (DONE): 20
   ⏳ Pendientes (PENDING): 51

📈 Progreso global: 28.2% (20/71)

📋 Desglose por tipo:
   PROJECT   :  4/13 completados (31%) — 9 pendientes
   EXERCISE  :  6/41 completados (15%) — 35 pendientes
   LESSON    :  8/14 completados (57%) — 6 pendientes
   QUIZ      :  2/3  completados (67%) — 1 pendiente
```

---

## Resumen de skills creadas

| # | Skill | Archivo | Endpoint | Función |
|---|-------|---------|----------|---------|
| 1 | Autenticación | `skills/fourgeeks-auth/SKILL.md` | `GET /v1/admissions/user/me` | Verifica token y sesión activa |
| 2 | Proyectos | `skills/fourgeeks-proyectos/SKILL.md` | `GET /v1/assignment/user/me/task?task_type=PROJECT` | Lista proyectos con estado |
| 3 | Trabajo pendiente | `skills/fourgeeks-pendientes/SKILL.md` | `GET /v1/assignment/user/me/task?task_status=PENDING` | Tareas pendientes agrupadas |
| 4 | Resumen de progreso | `skills/fourgeeks-progreso/SKILL.md` | `GET /v1/assignment/user/me/task` | Métricas globales de avance |

## Notas técnicas

- **Token seguro**: almacenado en el secret store protegido de OpenClaw (write-only, nunca hardcodeado, nunca visible en logs). Se inyecta como sentinel opaco mediante el egress proxy.
- **Proxy de egress**: activado para inyección automática del token en comandos `curl` hacia el host `breathecode.herokuapp.com`.
- **Academy ID**: 6 (4Geeks Madrid).
- **Cohorte activo**: spain-aie-devs-pt-1 (id: 1838).