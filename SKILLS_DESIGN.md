# SKILLS_DESIGN.md - Diseño de las skills de Mimo

Estas skills nacen de tareas que repito cada semana en el bootcamp: apuntar lo que aprendo (y los errores que cometo) y reservar tiempo de estudio en el calendario. Solo usan servicios **ya conectados**: Google Docs, Google Calendar (vía Zapier MCP) y Telegram.

---

## Skill 1: `diario-aprendizaje` 📓

### 1. ¿Qué hace esta skill?
Convierte unas notas rápidas de lo que aprendí hoy en una entrada de diario ordenada y la guarda como un Google Doc nuevo.

### 2. ¿Qué input necesita el agente?
- **Lo que le doy (por Telegram):** 3-6 puntos sueltos en texto libre sobre lo que aprendí, los errores que tuve y cómo los resolví. Ejemplo: *"diario: hoy vi operadores compuestos en TS, me equivoqué poniendo `:` en vez de `=`, y console.log = (...) rompe todo"*.
- **Lo que ya sabe por los 5 archivos:**
  - `USER.md`: que estoy en el bootcamp de AI Engineering y qué tecnologías veo.
  - `SOUL.md`: que debe explicar los errores con palabras sencillas y un ejemplo.
  - `TOOLS.md`: el formato de título de Docs y que la zona horaria es Europe/Madrid (para la fecha).
- **Si falta algo:** si solo escribo "diario" sin contenido, pregunta *"¿Qué aprendiste hoy?"* antes de crear nada.

### 3. ¿Cómo es un buen output?
- **Destino:** un Google Doc nuevo titulado `Diario de aprendizaje — <tema principal> — YYYY-MM-DD`.
- **Formato del documento:**
  1. 🎯 Qué aprendí (2-4 conceptos, una línea cada uno)
  2. 🐞 Errores y cómo los resolví (error → causa → solución, con un mini ejemplo de código)
  3. 💡 Regla para recordar (una frase)
  4. ⏭️ Qué repasar mañana
- **Confirmación en Telegram:** ✅ + título + 🔗 enlace al documento.
- **Sabré que funciona si:** el documento aparece en Google Docs de la cuenta de pruebas, con las 4 secciones, la fecha correcta y mis errores explicados en tono sencillo (no un resumen genérico).

---

## Skill 2: `sesion-estudio` 📅

### 1. ¿Qué hace esta skill?
Crea un bloque de estudio en Google Calendar a partir de una frase en lenguaje natural, aplicando mis valores por defecto.

### 2. ¿Qué input necesita el agente?
- **Lo que le doy (por Telegram):** una frase como *"sesión de TypeScript el jueves por la tarde, 2 horas, para repasar interfaces"*.
- **Lo que ya sabe por los 5 archivos:**
  - `USER.md`: zona horaria Europe/Madrid y que prefiero estudiar por la tarde (a partir de las 18:00).
  - `TOOLS.md`: calendario por defecto, emoji 📚 en el título, duración por defecto (1 h), recordatorio de 15 minutos y descripción con objetivo.
  - `SOUL.md` y `AGENTS.md`: si falta un dato clave, propone una opción razonable en **una sola pregunta**.
- **Interpretación:** "por la tarde" = 18:00; sin duración = 1 h; "el jueves" = el próximo jueves.

### 3. ¿Cómo es un buen output?
- **Destino:** un evento en Google Calendar (cuenta de pruebas).
- **Formato del evento:**
  - Título: `📚 Estudio: <tema>`
  - Hora correcta en **Europe/Madrid** (no UTC)
  - Descripción: objetivo de la sesión + mini checklist de 2-3 puntos
  - Recordatorio 15 min antes
- **Confirmación en Telegram:** ✅ título, 📅 día y hora (hora de España), 🔗 enlace al evento.
- **Sabré que funciona si:** el evento aparece en Calendar el día y a la hora correctos en hora de España, con el título y la descripción en mi formato, sin que yo haya tenido que dar la zona horaria ni el formato.

---

## Por qué estas dos

- Son tareas que **hago varias veces por semana** y que hoy me obligan a escribir instrucciones largas.
- Se complementan: una **registra** lo aprendido y la otra **planifica** cuándo repasarlo.
- Muestran claramente el efecto de los 5 archivos: tono didáctico (`SOUL.md`), mi contexto de estudio (`USER.md`), zona horaria y formatos (`TOOLS.md`) y límites (`AGENTS.md`).
