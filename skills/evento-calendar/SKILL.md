---
name: evento-calendar
description: Crea eventos en Google Calendar vía Zapier MCP y verifica en el servicio que se crearon con la fecha y hora correctas.
---

# Evento en Google Calendar (Zapier MCP)

Crea el evento en el calendario real de Telmo usando el conector de Zapier y **comprueba en el propio servicio** que salió como debe.

## Herramienta conectada

Conector: Zapier MCP (servidor `a3a8856f-90fd-4b17-aa7f-91024335f7cf`), acción `google_calendar_quick_add_event`. Si la sesión expone la acción de creación detallada (con campos separados de fecha/hora), prefiérela; si no, usa el Quick Add.

## Datos

**Obligatorios:** nombre, fecha, hora de inicio o "todo el día", repetición (sí/no + frecuencia si es sí: diaria, semanal, mensual, anual).

**Opcionales:** ubicación (física o enlace online), descripción/notas. Zona horaria: Europa (`USER.md`).

## Procedimiento

1. Extrae de la petición del usuario lo que ya esté. Si falta algún dato obligatorio, pídelo de forma amigable, de uno en uno.
2. Muestra un resumen y espera confirmación explícita. **Nunca crees el evento sin ella** (`AGENTS.md`: las acciones outbound se confirman antes).
3. Antes de llamar a la herramienta, convierte fecha y hora a los únicos formatos que el Quick Add parsea bien:
   - Fecha: `DD/MM/YYYY` (ej. `15/10/2026`).
   - Rango de horas con guion: `HH:MM-HH:MM` (ej. `14:00-14:30`); sin rango si es todo el día.
   - Texto del evento: `nombre DD/MM/YYYY HH:MM-HH:MM`, sin nada más.
   - Cualquier otro formato (inglés, ISO, "mañana") lo interpreta como **hoy** y contamina el título.
4. Llama a `google_calendar_quick_add_event` con el texto ya convertido.
5. **Verifica en el servicio:** revisa la respuesta de la herramienta y comprueba que el evento quedó con la fecha y hora pedidas y el título limpio. Si refleja otra fecha, hora equivocada o título con restos del texto, **avisa al usuario de inmediato** — este conector **no tiene acción de borrado**, así que el evento erróneo habrá que borrarlo a mano en Calendar.

## Resultado esperado

Confirmación en el chat con el evento visible en Google Calendar en la fecha y hora exactas. Criterio de éxito: la respuesta de Zapier refleja `DD/MM/YYYY` y `HH:MM-HH:MM` tal como se pidieron — cero eventos creados "hoy" cuando se pidió otra fecha, cero títulos con basura.

## Lecciones de 2026-10-14

Cuatro intentos fallidos dejaron ~5 eventos basura en `atdedits@gmail.com` por enviar formatos como `15 October 2026 14:00` o ISO. Si el usuario reporta un evento mal creado, pídele que lo borre manualmente en Calendar y déjalo anotado en la nota diaria (`memory/YYYY-MM-DD.md`).