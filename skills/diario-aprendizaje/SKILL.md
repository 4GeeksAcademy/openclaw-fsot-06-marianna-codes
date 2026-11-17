---
name: diario-aprendizaje
description: Convierte las notas rápidas de Marianna sobre lo que aprendió hoy en el bootcamp en una entrada de diario ordenada y la guarda como un Google Doc nuevo. Úsala cuando escriba "diario", "hoy aprendí", "apunta lo que aprendí" o algo parecido.
---

# Diario de aprendizaje 📓

Marianna está aprendiendo a programar en el bootcamp de AI Engineering (ver `USER.md`). Esta skill guarda lo que aprende cada día, con especial atención a los **errores** y a cómo los resolvió, para que no los repita.

## Input esperado

Texto libre por Telegram, normalmente 3-6 puntos sueltos. Ejemplo:

> diario: hoy vi operadores compuestos en TS, puse `:` en vez de `=` al declarar constantes, y `console.log = (...)` rompió todo

## Pasos

1. **Comprueba que hay contenido.** Si solo escribe "diario" o el mensaje no dice qué aprendió, pregunta una sola vez: *"¿Qué aprendiste hoy? Cuéntamelo en 3-4 puntos, con los errores si los hubo."* No crees nada hasta tener respuesta.
2. **Saca la fecha de hoy en Europe/Madrid** (por ejemplo, con `TZ=Europe/Madrid date +%F`). Nunca uses la fecha UTC.
3. **Elige el tema principal** (una o dos palabras: "TypeScript", "Git", "OpenClaw"…).
4. **Redacta la entrada** con este formato exacto, en español y con tono cercano (ver `SOUL.md`):

   ```
   🎯 Qué aprendí
   - <concepto 1, una línea>
   - <concepto 2, una línea>

   🐞 Errores y cómo los resolví
   - Error: <qué pasó>
     Causa: <por qué, explicado en sencillo>
     Solución: <qué se cambió>
     Ejemplo: <mini ejemplo de código antes → después>

   💡 Regla para recordar
   <una sola frase>

   ⏭️ Qué repasar mañana
   - <1-2 puntos>
   ```

   - Si no mencionó errores, escribe "Hoy sin errores 🎉" en esa sección. No inventes errores.
   - No añadas conceptos que ella no mencionó.
5. **Crea el Google Doc** con Zapier MCP (ver `TOOLS.md`):
   - Acción: Google Docs → Create Document from Text.
   - Título: `Diario de aprendizaje — <tema> — <YYYY-MM-DD>`.
   - Usa la cuenta de pruebas conectada (la predeterminada).
6. **Confirma por Telegram** en un mensaje corto:

   ```
   ✅ Diario guardado: <título>
   🔗 <enlace al documento>
   💡 <la regla para recordar>
   ```

## Límites

- No compartas el documento con nadie ni lo envíes a terceros.
- No incluyas datos personales, tokens ni rutas de archivos de configuración.
- Si Zapier falla, dilo claramente y no digas que el documento está creado.
