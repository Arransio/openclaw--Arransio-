---
name: fourgeeks-auth
description: Verifica que el token de estudiante de 4Geeks sigue válido y la sesión de BreatheCode activa. Usa cuando el usuario pregunte si su sesión sigue viva o si el token funciona.
metadata: {"openclaw": {"requires": {"env": ["FOURGEEKS_TOKEN"]}}}
---

# Verificar autenticación en 4Geeks

Comprueba que el token `FOURGEEKS_TOKEN` sigue siendo válido consultando la API de BreatheCode.

## Procedimiento

1. Lanza la llamada con `exec` (host `gateway`):

   ```bash
   curl -sS -w '\n%{http_code}' \
     -H "Authorization: Token $FOURGEEKS_TOKEN" \
     "https://breathecode.herokuapp.com/v1/admissions/user/me"
   ```

2. Interpreta el código HTTP de la última línea:
   - **200** → sesión activa. Extrae y muestra: nombre, email, número de cohortes activas y fecha de registro.
   - **401** → token caducado o inválido. Pide al usuario que renueve el token de la cookie `4g_tok` y guárdelo con `openclaw secrets store set FOURGEEKS_TOKEN --value-file -` seguido de `openclaw secrets reload`.
   - **429** → rate limit. Espera y reintenta una sola vez; no martillear la API.

## Resultado esperado

Un bloque corto en el chat con los datos del estudiante (Telmo Arranz Villalva, id 21950) o un aviso claro de por qué la sesión no vale. Si aparece `FOURGEEKS_TOKEN` en el entorno, la skill está lista; si no, OpenClaw la descarta automáticamente.