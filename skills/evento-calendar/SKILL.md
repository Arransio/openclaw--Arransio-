---
name: evento-calendar
description: 'Skill para procesar solicitudes del usuario y estructurarlas para crear eventos en Google Calendar de forma automática a través del chatbot.'
---

### Instrucciones de Funcionamiento:
Cuando el usuario solicite crear un evento, el chatbot debe verificar si se han proporcionado los siguientes datos obligatorios y opcionales:

1. **Datos obligatorios:**
   - **Nombre del evento:** (Ej: Reunión de equipo, Cumpleaños de Ana)
   - **Fecha:** (Ej: 25 de octubre de 2026)
   - **Momento/Horario:** 
     - Si es "todo el día", marcar la opción de día completo.
     - Si tiene hora específica, solicitar **Hora de inicio** y (opcionalmente) **Hora de fin o duración**.
   - **Repetición:** Sí o No (si es Sí, preguntar la frecuencia: diario, semanal, mensual, anual).

2. **Datos opcionales (preguntar solo si es relevante o para refinar):**
   - **Ubicación:** (Lugar físico o enlace online).
   - **Descripción/Notas:** (Detalles adicionales del evento).

3. **Flujo de interacción:**
   - Si falta algún dato obligatorio (nombre, fecha, hora/todo el día o repetición), el chatbot debe pedírselo al usuario de forma amigable antes de proceder.
   - Una vez recopilados todos los datos, el chatbot debe mostrar un resumen al usuario para que confirme antes de enviarlo a la API de Google Calendar.
   - Ejemplo de resumen: 
     "Voy a crear el evento **[Nombre]** para el día **[Fecha]** a las **[Hora]**, con repetición **[Sí/No]**. ¿Es correcto?"
