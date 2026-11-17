# TOOLS.md - Servicios conectados y cómo usarlos

## Resumen

| Servicio | Vía | Acción disponible | Para qué |
|---|---|---|---|
| Telegram | Canal de OpenClaw (`@mimo_cuao_bot`) | Recibir y enviar mensajes | Hablar con Marianna, confirmar tareas |
| Google Docs | Zapier MCP (`mcporter`, servidor `zapier`) | Create Document from Text | Crear documentos nuevos |
| Google Calendar | Zapier MCP (`mcporter`, servidor `zapier`) | Create Detailed Event (y actualizar eventos) | Crear o mover eventos |

**No conectados:** Gmail, Google Drive, Google Tasks, GitHub. Si Marianna pide algo de estos servicios, dile que no está conectado. **No inicies ninguna conexión nueva** (ver `AGENTS.md`).

## Cómo llamar a Zapier

- Usa `mcporter` con el servidor `zapier` (configuración en `config/`).
- Antes de ejecutar una acción, usa `inspect_zapier_actions` para obtener el `tool_name` y los parámetros exactos. No adivines nombres de acciones.
- Para crear: `execute_zapier_write_action`.
- La cuenta conectada (por defecto) es la **cuenta de pruebas** `mimo.pruebas.cuao@gmail.com`. No uses otra.

## Valores por defecto

### Google Calendar
- **Calendario:** el principal de la cuenta de pruebas.
- **Zona horaria:** `Europe/Madrid`, siempre.
- **Duración por defecto:** 1 hora si no se indica.
- **Título:** empieza con un emoji según el tipo: 📚 estudio, 🛠️ práctica o ejercicio, 📝 repaso.
- **Descripción:** incluye el objetivo de la sesión y, si existe, el enlace al documento relacionado.
- **Recordatorio:** 15 minutos antes.

### Google Docs
- **Título:** `<Tipo> — <Tema> — YYYY-MM-DD` (ej. `Diario de aprendizaje — TypeScript — 2026-11-17`).
- **Formato:** encabezados claros, listas cortas y una sección final "Próximos pasos".
- Al crear un documento, devuelve siempre el enlace.

### Telegram
- Confirma cada tarea con: ✅ qué hiciste, 🔗 enlace, ⏭️ qué falta (si falta algo).
- Mensajes cortos. Sin tablas (Telegram no las muestra bien): usa listas.
