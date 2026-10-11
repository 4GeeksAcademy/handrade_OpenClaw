# Workspace de Bery 🐻

Asistente personal de HeC (Héctor): configuración, skills y documentación del agente OpenClaw.

## Skills

Skills de OpenClaw (carpetas con `SKILL.md`), no skills de Zapier.

| Skill | Qué hace | Cómo activarla |
|---|---|---|
| `learning-journal` | Registra lo que HeC aprendió hoy como entrada fechada (CDMX) al Google Doc "Diario de aprendizaje - HeC", creándolo si no existe. | Escribir por Telegram algo como "esto aprendí hoy: ..." o "anota en mi diario: ...". También con `/skill learning-journal`. |
| `smart-calendar-event` | Convierte una descripción en lenguaje natural en una propuesta de evento (fecha/hora en CDMX, respetando 9:00-18:00) y, solo tras la confirmación de HeC, lo crea en su calendario principal. | Describir el evento por Telegram, ej. "sesión de estudio el jueves por la tarde, dos horas, con recordatorio". También con `/skill smart-calendar-event`. |

## Cómo reflejan los 5 archivos de configuración

- **IDENTITY.md** — las skills hablan como Bery: español de México, de tú, con 🐻.
- **SOUL.md** — actúo primero cuando es seguro y reversible; ante incertidumbre propongo lo razonable o pregunto (una pregunta a la vez).
- **AGENTS.md** — las skills paran y preguntan antes de crear/modificar/borrar eventos o documentos; los datos de `USER.private.md` solo se usan si HeC los pide; la excepción de la regla 8 cubre únicamente AÑADIR entradas al diario.
- **USER.md** — zona horaria CDMX (UTC-6), horario laboral 9:00-18:00, trato de tú.
- **TOOLS.md** — solo se usan Google Docs y Google Calendar (vía Zapier, cuenta Google principal conectada) y Telegram. Gmail, Drive y Tasks no están habilitados y no se usan.

## Configuración y privacidad

- Se renovó la autorización caducada de Zapier y la clave del modelo; no se añadió ningún servicio nuevo.
- `USER.md` del repo está generalizado (sin datos personales). Los datos reales de HeC viven en `USER.private.md`, un archivo local ignorado por git que nunca se commitea (regla 6 de AGENTS.md).

## Repo

- Rama `main`, origen en GitHub. Sin push automático: HeC revisa y sube.
- Commits con Conventional Commits (`feat`, `fix`, `docs`, `chore`).