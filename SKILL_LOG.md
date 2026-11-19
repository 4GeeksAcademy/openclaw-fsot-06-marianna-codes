# SKILL_LOG.md — Mi Asistente 4Geeks (Mimo 🤖)

Registro de las skills que construí **conversando con mi agente OpenClaw (Mimo)**, desde Telegram, para que siga mi progreso en el curso de 4Geeks a través de la API de estudiantes (BreatheCode).

- **Agente:** Mimo (OpenClaw 2026.10.9) en mi VPS, conectado a Telegram.
- **API:** `https://breathecode.herokuapp.com` · Documentación usada: *Student API Calls Reference* (enlace "Llamadas API (español)" del enunciado).
- **Enfoque:** una skill por acción de la API, todas de **solo lectura**.

---

## 🔐 Cómo se guarda el token

- El token de estudiante (cookie `4g_tok` de learn.4geeks.com) está en `~/.openclaw/.env` como `TOKEN_4GEEKS`, con permisos `600` (solo root). Este archivo está **fuera del workspace/repositorio**.
- Las skills usan la **variable de entorno** `$TOKEN_4GEEKS` en la cabecera `Authorization: Token ...`. Nunca leen el `.env` ni muestran el valor.
- **Nunca** envié el token por el chat. Mimo intentó abrir el "campo seguro" de credenciales de OpenClaw, pero no está disponible porque el Control UI solo escucha en loopback. Usamos la variable de entorno, que el Gateway ya tenía cargada.
- **Verificación:** busqué el token en todos los archivos del workspace y de las skills sin mostrarlo:

  ```bash
  set -a; . ~/.openclaw/.env; set +a
  grep -rlF "$TOKEN_4GEEKS" ~/.openclaw/workspace ~/.openclaw/skills 2>/dev/null || echo "✅ Token no encontrado en ningún archivo"
  ```

  Resultado: `✅ Token no encontrado en ningún archivo`.

---

## 🗣️ Conversación de descubrimiento

**Mi prompt inicial:**
> Quiero darte la habilidad de conectarte a mi cuenta de 4Geeks usando mi token de estudiante, sin que tenga que desarrollar código de mi parte. ¿Qué debemos hacer? El token ya está guardado en el VPS en ~/.openclaw/.env como TOKEN_4GEEKS, así que no me lo pidas por el chat. La documentación de la API está aquí: https://breathecode.herokuapp.com/asset/internal-link?id=3612&path=content/projects/openclaw-integration/STUDENT_API_CALLS_REFERENCE.es.md

**¿Qué sugirió Mimo?**
- Leyó la guía de la API por su cuenta.
- Empezar solo con consultas de **lectura**: perfil, cohortes, tareas, actividad y recursos educativos. No entregar ni modificar tareas.
- **No abrir ni leer** `~/.openclaw/.env`, y usar el almacén protegido de secretos de OpenClaw con acceso limitado a `breathecode.herokuapp.com`.
- Probar primero con una consulta sencilla y comprobar que la respuesta coincide con mi cuenta.

**¿Qué información me pidió?**
- Mi **autorización** para configurar la skill de solo lectura y registrar `TOKEN_4GEEKS` por un paso seguro (nunca por el chat).

**Decisiones que tomamos:**
- Mimo proponía **una sola skill** para todo. Le pedí **una skill separada por cada acción de la API**, empezando solo por la autenticación.
- Como el campo seguro no estaba disponible, usamos la variable de entorno `TOKEN_4GEEKS` ya cargada en el Gateway.

---

## Skill 1 — Autenticar (`4geeks-verify-session`)

- **Prompt en lenguaje natural:**
  > Sí, te autorizo a configurar skills de solo lectura. Pero con dos condiciones: 1. Quiero una skill separada por cada acción de la API, no una sola para todo. Empecemos solo por la primera: una skill que verifique que mi token es válido y que la sesión está activa. 2. Para el secreto, ¿puedes usar como origen la variable de entorno TOKEN_4GEEKS que ya está cargada? Si de verdad necesitas que lo introduzca en un campo seguro, explícame exactamente dónde aparece ese campo y cómo accedo a él. Nunca por el chat.
- **Qué hace:** comprueba que el token es válido haciendo una petición de lectura autenticada a mi perfil. No muestra datos personales.
- **Endpoint:** `GET /v1/admissions/user/me` (sin filtros).
- **Resultado de prueba (1er intento):**
  > […] 4Geeks respondió **HTTP 401**: el endpoint no aceptó el token, así que no puedo confirmar que sea válido.
- **Problema y solución:**
  - Comprobé el `.env` sin mostrar el token: longitud 40, sin `\r` ni comillas. El formato era correcto.
  - Probé con `curl` directamente y seguía dando 401, así que **el token había caducado**.
  - Cerré sesión en learn.4geeks.com, volví a entrar, copié el nuevo `4g_tok`, lo actualicé en el `.env` y comprobé con `curl` → **HTTP 200**. Reinicié el Gateway (`openclaw gateway restart`) para que cargara el token nuevo.
  - También limpié el historial de la terminal (`history -c && history -w`), porque había pegado el token en un comando.
- **Resultado de prueba (final):**
  > Sí: la comprobación con `4geeks-verify-session` devolvió **HTTP 200**. El token permite acceder ahora al endpoint autenticado de 4Geeks. […] No mostré el token ni datos personales.
- **¿Funcionó?** ✅

---

## Skill 2 — Obtener mis proyectos (`4geeks-list-projects`)

- **Prompt en lenguaje natural:**
  > Perfecto. Ahora quiero una segunda skill, separada de la anterior: que obtenga la lista de proyectos que tengo asignados en 4Geeks con su estado actual (pendiente, entregado o calificado). Usa solo el endpoint necesario para eso y muéstrame el resultado como una lista corta.
- **Qué hace:** lista mis proyectos asignados agrupados por estado.
- **Endpoint:** `GET /v1/assignment/user/me/task` con `task_type=PROJECT`, `limit=100`, `offset=N` (recorre todas las páginas).
- **Resultado de prueba:**
  > Encontré **21 asignaciones**: 3 pendientes y 18 en estado `DONE`. En la respuesta no aparece ninguna como calificada (`APPROVED` o `REJECTED`); `DONE` significa completada por ti, pero no confirma que ya esté calificada.
  >
  > **Pendientes:** AgentHub Admin Panel Specs and Prompt-Driven Prototype · Milestone 2 — Building Scripts to Automate Tasks · My 4Geeks Assistant — Teaching OpenClaw to Track Your Progress
  >
  > **Completadas (DONE):** Cinema Seat Manager in TypeScript, Connect Your Agent: Telegram, Google Drive & Calendar, My Agent, My Way…, Data modeling and class diagrams, entre otras (18 en total).
- **Observación:** Mimo distingue correctamente *entregado* (`task_status=DONE`) de *calificado* (`revision_status=APPROVED/REJECTED`) y detectó un duplicado (Milestone 0 aparece dos veces).
- **¿Funcionó?** ✅ (los 3 pendientes coinciden con la plataforma)

---

## Skill 3 — Obtener trabajo pendiente (`4geeks-pending-deliveries`)

- **Prompt en lenguaje natural:**
  > Genial. Ahora una tercera skill separada, que responda solo a "¿qué me falta por entregar?": que consulte únicamente mis tareas pendientes (filtrando en la API, no descargando todo), y para cada una me diga el nombre, el tipo (proyecto, ejercicio, lección...) y la fecha límite si la API la tiene. Ordénalas por la más urgente primero.
- **Qué hace:** devuelve solo lo que me falta completar, separado por tipo.
- **Endpoint:** `GET /v1/assignment/user/me/task` con `task_status=PENDING`, `limit=100`, `offset=N`.
- **Resultado de prueba:**
  > Consulté una sola vez la API, filtrando allí por tareas `PENDING`. Encontré **31 tareas**. Ninguna incluye fecha límite, así que no se pueden ordenar por urgencia; las dejo en el orden recibido.
  >
  > **Proyectos (3):** AgentHub Admin Panel Specs… · Milestone 2 — Building Scripts… · My 4Geeks Assistant…
  > **Ejercicios (28):** Understanding Mutability in TypeScript, Master TypeScript: Objects, Mastering Arrays in TypeScript, HTML Fundamentals, Connecting OpenClaw with 4Geeks Academy, entre otros.
- **Observación:** muchas lecciones de lectura aparecen como pendientes porque en 4Geeks solo se marcan como hechas si se completan en la plataforma.
- **¿Funcionó?** ✅

---

## Skill 4 — Resumen de progreso (`4geeks-progress-summary`)

- **Prompt en lenguaje natural:**
  > Muy bien. Ahora la cuarta skill separada: un resumen de mi progreso en el curso. Quiero ver cuánto llevo completado en porcentaje, separado por tipo (proyectos y ejercicios/lecciones), y cuántos me faltan. Si la documentación tiene un endpoint específico de progreso o de mi cohorte, úsalo; si no, calcúlalo con una sola consulta. Respuesta corta, en 5-6 líneas.
- **Qué hace:** calcula el porcentaje completado por tipo de tarea.
- **Endpoint:** `GET /v1/assignment/user/me/task` con `limit=100`, `offset=N`, sin filtros (para contar todo).
- **Resultado de prueba (1er intento):**
  > Proyectos: 88 % (15/17) · Ejercicios y lecciones: 67 % (56/83). **Importante:** la API indica 108 tareas en total, pero esta consulta devolvió 100 y señala que hay otra página. Las cifras son parciales.
- **Problema y solución — paginación:** los números no cuadraban con las skills 2 y 3 (3 vs 2 proyectos pendientes; 31 vs 29 tareas). Le pedí:
  > Los números de 4geeks-progress-summary no cuadran con las otras skills porque falta la segunda página. Actualiza la skill para que recorra todas las páginas de la API (o use el parámetro de límite que indique la documentación) hasta tener las 108 tareas, y vuelve a darme el resumen. Revisa también si 4geeks-list-projects y 4geeks-pending-deliveries necesitan la misma corrección.
- **Resultado de prueba (final):**
  > Actualicé la paginación en las tres skills. La consulta completa devolvió **108 tareas en 2 páginas**.
  > Proyectos: **86 %** completados (18/21); faltan 3.
  > Ejercicios y lecciones: **68 %** completados (59/87); faltan 28.
  > En total, quedan **31 tareas pendientes**.
- **¿Funcionó?** ✅ (ahora coincide con las skills 2 y 3)

---

## Skill 5 (extendida) — Información de mi cohorte (`4geeks-cohort-info`)

- **Necesidad que detecté:** quiero saber en qué punto del programa estoy y cuánto falta para terminar, sin entrar en la plataforma.
- **Prompt en lenguaje natural:**
  > Ahora una skill extra separada: quiero saber la información de mi cohorte en 4Geeks: nombre, fecha de inicio y fin, y en qué día o módulo del programa vamos. Usa el endpoint de la documentación que corresponda. Respuesta corta.
- **Qué hace:** muestra el nombre de mi cohorte activa, sus fechas y el día del programa.
- **Endpoints:** `GET /v1/admissions/user/me` (lista `cohorts`); si faltan datos, `GET /v1/admissions/academy/cohort/me` con la cabecera `Academy: <academy_id>`.
- **Resultado de prueba (1er intento):**
  > 4Geeks devolvió **HTTP 403**, tanto con el filtro de cohorte activa como sin él. No pude obtener el nombre, las fechas ni el módulo, y no voy a inventarlos.
- **Problema y solución:** el 403 indicaba que el endpoint de cohortes necesita el `academy_id` (como explicaba la teoría). Le pedí:
  > El 403 puede deberse a que ese endpoint necesita el academy_id. Prueba esto, en orden: 1. Usa el endpoint /v1/admissions/user/me (que ya funciona con 200): su respuesta incluye la lista "cohorts". Saca de ahí el nombre de mi cohorte, las fechas y el academy_id. 2. Si faltan datos (como el día o módulo actual), vuelve a intentar el endpoint de cohortes enviando también el academy_id como indique la documentación (por ejemplo, en la cabecera "Academy"). Actualiza la skill 4geeks-cohort-info con lo que funcione y muéstrame el resultado. No muestres mi email ni datos personales.
- **Resultado de prueba (final):**
  > Cohorte: spain-aie-pt-6 · Inicio: 5 de octubre de 2026 · Fin: 16 de abril de 2027 · Programa: día/módulo 19
- **¿Funcionó?** ✅

---

## Skill 6 (extendida) — Buscar ejercicios por tema (`4geeks-search-resources`)

- **Necesidad que detecté:** cuando me atasco con un tema, quiero encontrar rápido ejercicios de práctica de 4Geeks sin buscar a mano en la plataforma.
- **Prompt en lenguaje natural:**
  > Ahora la última skill extra, separada: cuando te diga un tema (por ejemplo "TypeScript" o "arrays"), busca en los recursos educativos de 4Geeks ejercicios o lecciones sobre ese tema y dame una lista corta (máximo 5) con el título y el enlace. Usa solo el endpoint de recursos de la documentación. Pruébala con el tema "TypeScript".
- **Qué hace:** busca recursos educativos de 4Geeks por tema y devuelve título y enlace.
- **Endpoint:** `GET /v1/registry/asset` con `asset_type=EXERCISE`, `difficulty=BEGINNER`, `limit=100` y `technologies=<slug>` o `like=<tema>`.
- **Resultado de prueba (1ª versión):**
  > 5 ejercicios: Pruebas Unitarias en TypeScript · Desarrollo Offline-First y Persistencia de Datos en React Native con TypeScript · Offline-First and Data Persistence in React Native with TypeScript · Integración nativa de la cámara en React Native CLI con TypeScript · Native Camera Integration in React Native CLI with TypeScript
- **Iteración:** había duplicados en dos idiomas y temas avanzados. Le pedí:
  > Mejora 4geeks-search-resources: muestra solo recursos en español, sin duplicados en otro idioma, y prioriza los de nivel principiante si la API permite filtrar por dificultad. Pruébala otra vez con "TypeScript".
- **Resultado de prueba (2ª versión):**
  > Ahora filtra por español, quita duplicados y pide nivel principiante. La API devolvió un recurso marcado como principiante: Ganchos avanzados y composición en React Native con TypeScript (título traducido; la API lo devuelve en inglés).
- **Limitación detectada:** con los filtros combinados (español + principiante) quedan muy pocos resultados, y la etiqueta de dificultad de la API no siempre coincide con el contenido. Para una próxima versión: hacer opcional el filtro de dificultad.
- **¿Funcionó?** ✅ (con la limitación indicada)

---

## 📚 Lo que aprendí

- **Un token caduca:** un 401 con el formato correcto suele significar que el token ya no es válido. Al volver a iniciar sesión se genera uno nuevo.
- **Paginación:** si los totales de dos skills no coinciden, hay que comprobar si la API devuelve los datos por páginas (`limit` y `offset`).
- **401 vs 403:** 401 significa "no sé quién eres"; 403 significa "sé quién eres, pero te falta permiso". En 4Geeks, algunos endpoints necesitan el `academy_id` en la cabecera `Academy`.
- **Seguridad:** el token solo vive en `~/.openclaw/.env`. Las skills usan la variable de entorno y nunca se pega en el chat, en las skills ni en el repo.
- **Una skill = una acción:** separar las skills hizo más fácil detectar y corregir cada error por separado.
