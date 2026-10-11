---
name: smart-calendar-event
description: Convierte una descripción en lenguaje natural de un evento en una propuesta concreta y la crea en el calendario de HeC solo tras su confirmación.
---

# Smart Calendar Event

Convierte lo que HeC describe en lenguaje natural ("sesión de estudio el jueves por la tarde, dos horas") en un evento concreto, se lo propone y **solo tras su confirmación** lo crea en su calendario principal vía Zapier.

## Antes de actuar: lee la configuración (una vez por sesión si no cambió)

1. `USER.md` — zona horaria CDMX (UTC-6), horario laboral 9:00-18:00, trato de tú, 🐻.
2. `SOUL.md` — propongo lo más razonable ante incertidumbre, sin inventar; si es ambiguo, pregunto (una a la vez).
3. `AGENTS.md` — reglas de privacidad (1-6), frenos de mano (7-11), configuración (12-13), estilo (14-16).
4. `TOOLS.md` — defaults: calendario principal de la cuenta Google conectada en Zapier; nunca repetir correos ni datos personales en mensajes.

## Cuándo se activa

HeC describe un evento en lenguaje natural, por ejemplo:
- "sesión de estudio el jueves por la tarde, dos horas, con recordatorio"
- "reunión con proveedores mañana a las 10"
- "junta de directorio el 20 a las 9, una hora"

## Flujo (orden estricto)

1. **Recibe la descripción** y extrae: título, fecha (relativa o absoluta), hora o franja, duración, recordatorio si lo menciona.
2. **Resuelve fecha y hora en America/Mexico_City (UTC-6)**. Para expresiones relativas ("el jueves", "mañana") usa el día más próximo en el futuro desde hoy en CDMX. No asumas la zona del servidor.
3. **Respeta la jornada 9:00-18:00**: si HeC no da hora, propón dentro de esa franja (ej. 16:00-18:00 para "tarde"). Si HeC pide explícitamente algo fuera de jornada, respétalo — él manda.
4. **Formatea la propuesta** (ver plantilla) y envíala por Telegram. **NUNCA crees el evento antes de su "sí"** (regla 9).
5. **Ante una sola confirmación ("sí", "dale", "ok")**, crea el evento con `google_calendar_create_detailed_event` (acción `detailed_event`):
   - `calendarid` = calendario principal de la cuenta conectada
   - `summary` = título del evento
   - `start__dateTime` / `end__dateTime` = fecha y hora en CDMX (ISO 8601)
   - `reminders__useDefault` = `no` + `reminders_methods` = `popup` + `reminders_minutes` (30 por defecto si HeC no especificó)
   - `visibility` = `default`, `transparency` = `opaque` (a menos que HeC pida otra cosa)
6. **Confirma en Telegram** con el detalle del evento creado (fecha, hora, recordatorio) y enlace si está disponible.

**Antes de cada llamada a Zapier:** usa `inspect_zapier_actions` para confirmar el nombre exacto de la acción y sus parámetros. Nunca adivines nombres.

## Plantilla de propuesta (output, antes de crear)

```
¿Te confirmo este evento?
📅 <título>
🗓️ <día de semana> <fecha>, <hora inicio>–<hora fin> (CDMX)
⏰ Recordatorio <N> min antes
¿Lo creo?
```

**Ejemplo** (si HeC dice: "sesión de estudio el jueves por la tarde, dos horas, con recordatorio", y el jueves más próximo es el 15 de octubre):

```
¿Te confirmo este evento?
📅 Sesión de estudio
🗓️ Jueves 15 de octubre, 16:00–18:00 (CDMX)
⏰ Recordatorio 30 min antes
¿Lo creo?
```

## Plantilla de confirmación (Telegram, después de crear)

```
✅ Evento creado
📅 <título>
🗓️ <día de semana> <fecha>, <hora inicio>–<hora fin> (CDMX)
⏰ Recordatorio <N> min antes
```

Agrega el enlace al evento si la API lo devuelve.

## Casos límite

- **Input vacío o ambiguo:** haz **UNA sola pregunta** (regla 14). Ej.: "¿Qué día y a qué hora?" Nunca inventes fecha/hora si no hay forma razonable de resolverlas.
- **Día ambiguo ("el jueves"):** elige el jueves más próximo en CDMX y muéstralo con fecha explícita en la propuesta para que HeC verifique.
- **Fecha en el pasado:** si la fecha ya pasó en CDMX, dilo y propón la siguiente ocurrencia o pregunta (una sola pregunta).
- **Fuera de 9:00-18:00:** si HeC no especificó hora, propón dentro de jornada. Si HeC la pidió explícitamente fuera, respétalo. Si el evento cruza el final de jornada (16:00-19:00), ajústalo a 18:00 y menciónalo en la propuesta.
- **Choca con otro evento:** revisa disponibilidad con `google_calendar_find_events` si es necesario y avisa: "Ya tienes X a esa hora; ¿lo muevo a las 17:00?" (una sola pregunta, regla 14).
- **HeC pide editar o borrar eventos existentes:** **detente y pide confirmación** (regla 9).
- **HeC pide mover a otro calendario:** pregúntale antes (regla 9); el default es el calendario principal.
- **Falla la conexión con Zapier:** repórtalo tal cual (regla 16), no finjas que se creó; propón reintentar.

## Verificación final

1. Nada se creó antes del "sí" de HeC.
2. El evento existe en el calendario principal con título, fecha, hora y recordatorio correctos (zona CDMX).
3. Se envió confirmación con el detalle del evento.
4. No se modificó ni borró ningún otro evento.