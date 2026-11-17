---
name: sesion-estudio
description: Crea un bloque de estudio del bootcamp en Google Calendar a partir de una frase en lenguaje natural ("sesión de TypeScript el jueves por la tarde, 2 horas"), aplicando la zona horaria y los formatos por defecto de Marianna. Úsala cuando pida reservar, bloquear o planificar tiempo de estudio o práctica.
---

# Sesión de estudio 📅

Marianna reserva tiempo para estudiar varias veces por semana. Esta skill entiende frases naturales y crea el evento ya bien hecho, sin que ella tenga que dar zona horaria, formato ni recordatorios.

## Input esperado

Una frase por Telegram. Ejemplos:

> sesión de TypeScript el jueves por la tarde, 2 horas, para repasar interfaces
> bloquea mañana 1h para practicar Git

## Cómo interpretar la frase

- **Zona horaria:** siempre `Europe/Madrid` (ver `USER.md` y `TOOLS.md`).
- **"por la tarde"** = 18:00. **"por la mañana"** = 10:00. **"por la noche"** = 20:00.
- **Sin duración** = 1 hora.
- **"el jueves"** = el próximo jueves (si hoy es jueves y ya pasó la hora, el de la semana siguiente).
- **Tipo y emoji:** 📚 estudio (teoría o repaso de un tema), 🛠️ práctica o ejercicio (Learnpack, proyecto), 📝 repaso.

## Pasos

1. **Extrae:** tema, día, hora, duración y objetivo.
2. **Si falta el día o el tema**, haz **una sola pregunta** proponiendo una opción. Ejemplo: *"¿Te lo pongo el jueves a las 18:00?"*. Si solo falta la hora o la duración, usa los valores por defecto sin preguntar.
3. **Comprueba la fecha** con la fecha actual en Europe/Madrid (por ejemplo, `TZ=Europe/Madrid date`).
4. **Prepara el evento:**
   - **Título:** `<emoji> <Tipo>: <tema>`, por ejemplo `📚 Estudio: TypeScript`.
   - **Inicio y fin** en Europe/Madrid (no en UTC).
   - **Descripción:**
     ```
     🎯 Objetivo: <objetivo o "Avanzar en <tema>">
     ✅ Checklist:
     - <paso 1 concreto>
     - <paso 2 concreto>
     - Apuntar lo aprendido con "diario" al terminar
     ```
   - **Recordatorio:** 15 minutos antes.
5. **Crea el evento** con Zapier MCP (ver `TOOLS.md`): Google Calendar → Create Detailed Event, en el calendario principal de la cuenta de pruebas. Indica la zona horaria `Europe/Madrid` de forma explícita.
6. **Confirma por Telegram:**

   ```
   ✅ <título>
   📅 <día de la semana> <DD/MM>, <HH:MM>–<HH:MM> (hora de España)
   🔗 <enlace al evento>
   ```

## Límites

- No borres ni muevas eventos existentes sin preguntar (ver `AGENTS.md`).
- No invites a otras personas al evento.
- Si Zapier falla, dilo claramente y no digas que el evento está creado.
