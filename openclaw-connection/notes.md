# OpenClaw Connection – Mimo 🐶🤖

- Conecté mi agente OpenClaw (Mimo) a Telegram con un bot creado en BotFather (`openclaw channels add`) y aprobé mi usuario con `openclaw pairing approve`.
- Problema: al aprobar usé el telegramUserId en vez del código de la columna "Code"; lo solucioné usando el código correcto.
- Conecté Zapier MCP con el prompt oficial de Zapier: Mimo instaló mcporter y añadió el servidor.
- Problema: la autorización OAuth redirige a 127.0.0.1 (el VPS), que no es accesible desde mi navegador. Lo resolví ejecutando `mcporter auth zapier` en el VPS y enviando la URL del callback con `curl` desde una segunda terminal SSH.
- Mimo activó las acciones "Create Document from Text" (Google Docs) y "Create Detailed Event" (Google Calendar).
- Decisión de seguridad: al principio conecté mi Gmail personal por error; lo eliminé de Zapier, revoqué el acceso en Google y usé una cuenta de prueba dedicada.
- El flujo completo funciona: Mimo pide los datos que faltan, crea el documento y el evento de revisión, y confirma por Telegram.
- Nota: el evento se creó primero en UTC; le pedí a Mimo usar la zona Europe/Madrid y lo movió a las 18:00 hora de España sin duplicarlo.
- No se incluyen tokens, URLs de autorización ni datos personales en las capturas.
