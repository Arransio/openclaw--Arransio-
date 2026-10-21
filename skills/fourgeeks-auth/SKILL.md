---
name: fourgeeks-auth
description: "Verificar autenticación contra la API de 4Geeks/BreatheCode usando el token de estudiante almacenado. Responde preguntas como: ¿Mi sesión sigue activa? ¿El token sigue siendo válido?"
---

# Verificar autenticación en 4Geeks

## Descripción

Skill para comprobar que el token de estudiante de 4Geeks sigue siendo válido y que la sesión con la API de BreatheCode está activa.

## Endpoint utilizado

`GET /v1/admissions/user/me` — devuelve los datos del usuario autenticado, incluyendo id, email, nombre, academias vinculadas y cohortes.

## Procedimiento

1. Ejecuta una llamada autenticada al endpoint `/v1/admissions/user/me` usando el token almacenado en el secret store de OpenClaw (`FOURGEEKS_TOKEN`), que se inyecta como sentinel mediante el egress proxy.
   - Host del comando: `gateway`
   - URL: `https://breathecode.herokuapp.com/v1/admissions/user/me`
   - Header: `Authorization: Token $FOURGEEKS_TOKEN`

2. Verifica que la respuesta HTTP sea `200`.

3. Si la respuesta es correcta, extrae y muestra:
   - Nombre y email del estudiante
   - Número de cohortes activas
   - Fecha de registro en la plataforma

4. Si la respuesta es `401` o hay error de conexión, informa de que el token no es válido o la sesión ha expirado.

## Resultado esperado

La skill confirma que la autenticación funciona, mostrando los datos básicos del estudiante para verificar que el token corresponde a la cuenta correcta.