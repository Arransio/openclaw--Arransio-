# TOOLS.md — Herramientas y Entorno

Notas locales de herramientas y entorno. Los procedimientos detallados viven en `skills/*/SKILL.md`; aquí solo queda lo operativo.

## OpenClaw CLI

- **Secret store** (token 4Geeks):
  ```bash
  openclaw secrets store set FOURGEEKS_TOKEN --kind secret --value-file -   # valor por stdin, sin \n
  openclaw secrets store set FOURGEEKS_TOKEN --allow-host breathecode.herokuapp.com
  openclaw secrets reload                                                    # tras cada escritura
  ```
- **Proxy de egress:** `openclaw config set secrets.egressProxy.enabled true --strict-json` + `openclaw gateway restart`.
- El token se usa como sentinel (`$FOURGEEKS_TOKEN`): el valor real solo sale hacia el host autorizado; nunca se pega en el chat.
- **Gotchas aprendidas:**
  - El CLI local apuntaba a puerto `4000` pero el gateway corre en `18789` (override `OPENCLAW_GATEWAY_PORT`); ya sincronizado en config (`gateway.port: 18789`).
  - `openclaw secrets store set` exige `--value-file -`; sin `--allow-host` el egress da 502.
  - Guardar con Enter pegado añade `\n` y rompe el token → usar siempre `printf '%s' "..." |`.
  - API de BreatheCode con logins repetidos devuelve `429` (rate limit): no martillear.
- **Automations:** `openclaw automations list --all`; actualizar scratch con `openclaw automations scratch <jobId> --set "..."`.

## API 4Geeks / BreatheCode

- Base: `https://breathecode.herokuapp.com`
- Header: `Authorization: Token $FOURGEEKS_TOKEN` (host: `breathecode.herokuapp.com`)
- Endpoints usados:
  | Método | Ruta | Uso |
  |--------|------|-----|
  | GET | `/v1/admissions/user/me` | verificar auth (skill `fourgeeks-auth`) |
  | GET | `/v1/assignment/user/me/task?task_type=PROJECT` | proyectos (skill `fourgeeks-proyectos`) |
  | GET | `/v1/assignment/user/me/task?task_status=PENDING&limit=100` | pendientes (skills `fourgeeks-pendientes`, `entregar-tarea`) |
  | PUT | `/v1/assignment/task/{task_id}` | actualizar URL de entrega (skill `entregar-tarea`) |
- Estudiante: Telmo Arranz Villalva (id 21950), cohort `spain-aie-devs-pt-1` (id 1838).

## Skills instaladas (`skills/`)

- `fourgeeks-auth` — verificar sesión/token.
- `fourgeeks-progreso` — % de avance general del curso.
- `fourgeeks-proyectos` — listar proyectos y su estado.
- `fourgeeks-pendientes` — qué falta por entregar.
- `entregar-tarea` — flujo interactivo de entrega con URL.
- `evento-calendar` — estructurar eventos para Google Calendar (datos obligatorios → resumen → confirmación → API).

## Git / Repositorio

- No hacer commit sin revisión previa del usuario.

## Formato por plataforma

- Discord/WhatsApp: listas en vez de tablas; en Discord envolver múltiples links en `<>`.
- WhatsApp: **negrita** o MAYÚS en lugar de headers.