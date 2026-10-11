# EVIDENCIA.md — Pruebas de Skills (resultados reales)

> Fecha de las pruebas: **2026-10-11**. Entradas reales de HeC por Telegram; resultados verificados por Bery con las respuestas reales de las APIs de Google Docs y Google Calendar.
> Las capturas se alojan en `docs/evidencia/` (rutas relativas) y fueron aportadas por HeC.

---

## Skill 1: learning-journal

### Prueba 1 — primera entrada (el Doc NO existía)

**Input (dictado real por Telegram, 10:14 UTC):**

> "Hoy aprendí a reactivarte y darte los primeros auxilios porque te desmayaste y no respondías."

**Output esperado (según SKILLS_DESIGN.md):** entrada con fecha de hoy en CDMX agregada al final del Doc "Diario de aprendizaje - HeC"; si el Doc no existe, crearlo y avisarlo.

**Output obtenido:**
- El Doc **no existía** → se creó con `google_docs_create_document_from_text` (acción `newtxtdocument`) y se avisó explícitamente ("creé el doc porque no existía").
- Contenido real del Doc (leído vía API tras la prueba):

  ```
  ## 2026-10-11 — Domingo

  - Reactivar a Bery y darle primeros auxilios cuando se desmayó y no respondía.
  ```

- **Los `##` del encabezado y los `-` de las viñetas salen literales** en el Doc (texto plano, no markdown renderizado) ✅
- **Fecha correcta:** 2026-10-11 es domingo y es la fecha de hoy en CDMX (UTC-6) ✅

**Cómo se refleja la configuración:** SOUL.md (actúo primero porque añadir al final es reversible; confirmación corta "Listo, agregado al diario 📝"); USER.md (zona CDMX, español, de tú); AGENTS.md regla 8 con su excepción (el dictado de HeC es la aprobación para AÑADIR; no se tocó nada existente); TOOLS.md (solo Google Docs vía Zapier, cuenta principal, sin repetir correos).

### Prueba 2 — segunda entrada (el Doc ya existía)

**Input (10:20 UTC):**

> "Anota en mi diario: hoy aprendí que un proyecto de agente se evalúa por el diseño de sus skills, no solo por que funcionen, y cómo conectar Zapier a OpenClaw desde un VPS sin navegador."

**Output esperado:** entrada fechada agregada al final, sin modificar lo anterior.

**Output obtenido:** el Doc **ya existía** → se usó `google_docs_append_text_to_document` (acción `append`, `newline: true`). Contenido real final del Doc (verificado vía API):

```
## 2026-10-11 — Domingo

- Reactivar a Bery y darle primeros auxilios cuando se desmayó y no respondía.

## 2026-10-11 — Domingo

- Un proyecto de agente se evalúa por el diseño de sus skills, no solo por que funcionen.
- Cómo conectar Zapier a OpenClaw desde un VPS sin navegador.
```

Ambas entradas con fecha correcta en CDMX; nada anterior se modificó ni borró ✅

**Capturas:**

| Qué muestra | Ruta relativa |
|---|---|
| Chat de Telegram: dictado de HeC + confirmación de Bery (diario) — **imagen idéntica a `03-evento-propuesta.png` (mismo MD5)** | `evidencia/01-diario-telegram.png` |
| Google Doc con las entradas del diario | `evidencia/02-diario-doc.png` |

**Limitación conocida:** cuando hay dos dictados el mismo día, el Doc muestra el encabezado de fecha repetido (`## 2026-10-11 — Domingo` aparece dos veces), porque `append` solo agrega al final y no detecta si ya existe una entrada con la misma fecha. Es lo que ocurrió en las pruebas 1 y 2 de hoy.

---

## Skill 2: smart-calendar-event

### Prueba — propuesta + creación solo tras confirmación

**Input (10:15 UTC):**

> "Sesión de estudio el jueves por la tarde, dos horas, con recordatorio"

**Output esperado (según SKILLS_DESIGN.md):** propuesta con título, fecha/hora en CDMX dentro de 9:00-18:00 y recordatorio; **nada se crea antes del "sí"** (regla 9).

**Output obtenido:**
1. **Disponibilidad:** se consultó el calendario principal para el jueves próximo (15/10) con `google_calendar_find_events` → sin conflictos.
2. **Propuesta enviada (antes de crear nada):**

   ```
   ¿Te confirmo este evento?
   📅 Sesión de estudio
   🗓️ Jueves 15 de octubre, 16:00–18:00 (CDMX)
   ⏰ Recordatorio 30 min antes
   ¿Lo creo?
   ```

3. **No se creó nada antes del "sí"** — lo confirma la secuencia real: propuesta → espera → HeC respondió "Crealo" → recién ahí se ejecutó `google_calendar_create_detailed_event` ✅
4. **Evento creado (verificado en la respuesta real de la API):**
   - Título: **Sesión de estudio**
   - Inicio: **2026-10-15T16:00:00-06:00** (America/Mexico_City)
   - Fin: **2026-10-15T18:00:00-06:00**
   - Duración: **2 horas**
   - Recordatorio: **popup 30 min antes**
   - Estado: `confirmed`, calendario principal ✅

**Cómo se refleja la configuración:** USER.md (zona CDMX y jornada 9:00-18:00 → "tarde" se resolvió como 16:00-18:00); AGENTS.md regla 9 (parar y preguntar antes de crear; se creó solo tras confirmación); SOUL.md (propuesta clara y una sola pregunta: "¿Lo creo?"); TOOLS.md (calendario principal de la cuenta conectada); tono de tú con 🐻 en el resumen de confirmación.

**Capturas:**

| Qué muestra | Ruta relativa |
|---|---|
| Captura de Telegram con la conversación del evento — **imagen idéntica a `01-diario-telegram.png` (mismo MD5)** | `evidencia/03-evento-propuesta.png` |
| Evento creado en Google Calendar | `evidencia/04-evento-calendar.png` |

Nota: `01` y `03` tienen exactamente el mismo hash (MD5 `cab379100958c196ee389050b06386dc`). Según la decisión de HeC, una sola captura muestra ambas conversaciones (diario y propuesta de evento) y no se reemplazó el archivo. No se pudo inspeccionar el contenido visual (el analizador de imágenes no estuvo disponible); la descripción se basa en los nombres de archivo y el contexto de las pruebas.

---

## Prueba de freno (opcional)

No se ejecutó una prueba de freno dedicada (p. ej. pedir editar/borrar un evento o documento existente). Como evidencia de que el freno opera, en la prueba 2 no se creó el evento hasta la confirmación explícita de HeC (regla 9). Queda disponible si HeC quiere probarla.

---

## Resultado de `openclaw doctor` (corrido el 2026-10-11)

Ejecutado como `printf 'No\n' | openclaw doctor` (se respondió **No** a deshabilitar skills; no se deshabilitó nada). Extracto de la salida:

```
◇  Startup optimization ──
  - NODE_COMPILE_CACHE is not set; repeated CLI runs can be slower on
    small hosts (Raspberry Pi/VM).
  - OPENCLAW_NO_RESPAWN is not set to 1; ...
◇  Session locks ──
  - Found 1 session lock file.
  - ~/.openclaw/agents/main/sessions/...jsonl.lock pid=... (alive) stale=no
◇  Security ──
  - WARNING: openclaw.json contains plaintext secret-bearing config fields.
    Paths: gateway.auth.token, channels.telegram.botToken
  - Run: openclaw security audit --deep
◇  Skills status ──
  Eligible: 19 | Missing requirements: 30 | Incompatible (platform, auto-skipped): 6 | Blocked by allowlist: 0
◇  Plugins ──
  Loaded: 50 | Imported: 0 | Disabled: 17 | Errors: 0
◇  Memory search ──
  Memory search is explicitly disabled (enabled: false).
Run "openclaw doctor --fix" to apply changes.
└  Doctor complete.   (exit 0)
```

Notas:
- **Sin errores críticos**; el doctor terminó con `exit 0`.
- Advertencias conocidas y no bloqueantes: secrets en texto plano en `openclaw.json` (sugiere migrar a SecretRefs) y sugerencias de optimización de arranque.
- Las skills propias se verificaron por separado con `openclaw skills info` (ambas `✓ Ready`, `Visible to model: yes`, `Available as command: yes`).