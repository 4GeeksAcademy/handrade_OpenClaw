# SKILLS_DESIGN.md — Diseño de Skills de Bery

> Documento de diseño previo a la implementación. Ninguna skill está implementada todavía.
> Autor: Bery 🐻 · Fecha: 2026-10-11 · Estado: pendiente de aprobación

---

## Criterio de selección

Elegí dos skills que (1) resuelven tareas reales y repetitivas de HeC, (2) usan **solo** los servicios ya conectados vía Zapier — Google Docs y Google Calendar, con Telegram como canal de entrada — y (3) respetan las reglas inamovibles de AGENTS.md: nada de APIs nuevas, nada de OAuth nuevo, y confirmación antes de cualquier acción irreversible.

El criterio fue simple: **máximo valor con cero configuración nueva**. Ambas skills convierten algo que HeC ya hace (dictar por Telegram) en algo que le ahorra pasos (escribir en el diario, crear eventos). Ninguna requiere Gmail, Drive ni Tasks, que no están habilitados.

---

# SKILL 1: learning-journal

## 1. ¿Qué hace?

Toma los puntos que HeC dicta por Telegram sobre lo que aprendió hoy y los agrega como una entrada fechada al Google Doc "Diario de aprendizaje - HeC", creándolo si no existe.

## 2. ¿Qué entrada necesita?

**Formato:** un mensaje de Telegram en lenguaje natural, normalmente con viñetas o puntos sueltos. No requiere estructura rígida.

**Ejemplo real:**
> "Hoy aprendí: 1) cómo bajar el cierre contable de 10 a 6 días con automatización, 2) la diferencia práctica entre OKR y KPI, 3) que el NPS sube cuando los GM tienen one-on-ones con agenda fija."

**Qué sé ya por los archivos de configuración:**
- IDENTITY.md: soy Bery, asistente con criterio, sin falsa energía.
- SOUL.md: actúo primero cuando es seguro y reversible; formato Telegram en español, párrafos cortos.
- AGENTS.md: regla 8 — no borro, muevo, renombro ni edito archivos o Google Docs sin confirmación. La excepción explícita de la regla 8 para la skill learning-journal respalda que mi dictado cuente como aprobación para AÑADIR una entrada al final del diario; editar o borrar entradas anteriores, o compartir el Doc, sigue requiriendo confirmación. Regla 12 — no configuro nada nuevo; regla 14 — una pregunta a la vez.
- USER.md: fecha de hoy se interpreta en CDMX (UTC-6); idioma español; el diario es "Diario de aprendizaje - HeC".
- TOOLS.md: el diario se crea/edita con Google Docs vía Zapier, cuenta Google principal conectada; no hay carpeta de Drive por defecto (Drive no está habilitado), el Doc se crea directamente.

## 3. ¿Cómo es un buen resultado?

**Formato:** una entrada con encabezado de fecha en español (ej. `## 2026-10-11 — Domingo`) seguida de la lista de puntos, agregada al final del Doc.

**Destino exacto:** Google Doc "Diario de aprendizaje - HeC" (cuenta Google principal vía Zapier). Si no existe, se crea con ese nombre y se avisa a HeC.

**Plantilla de ejemplo:**
```markdown
## 2026-10-11 — Domingo

- Cierre contable: de 10 a 6 días vía automatización.
- OKR vs KPI: diferencia práctica.
- NPS sube con one-on-ones de agenda fija con los GM.
```

**Criterios verificables de éxito:**
1. El Doc existe (creado o ya existente) con el nombre exacto "Diario de aprendizaje - HeC".
2. La nueva entrada tiene la fecha de hoy en zona CDMX (UTC-6).
3. Los puntos dictados por HeC están todos presentes, en español, sin inventar contenido.
4. Se confirma a HeC en Telegram con un mensaje corto: "Agregado al diario 📝" (y "Creé el doc" si fue nuevo).
5. No se modificó ni borró nada más del documento.

## 4. Por qué esta tarea, esta entrada y este formato

- **Por qué esta tarea:** HeC está en modo aprendizaje continuo (objetivo de afianzarse como director, construcción de plataforma IA). Un diario de aprendizaje refuerza su hábito y me da contexto acumulado para ayudarle mejor. Es de las pocas tareas con valor diario real y bajo riesgo.
- **Por qué esta entrada:** el dictado libre por Telegram es el canal natural de HeC — lo usa todo el día y no requiere abrir otra app. Forzar una plantilla rígida mataría el hábito.
- **Por qué este formato de resultado:** encabezado de fecha + viñetas es legible, consistente y permite crecer el doc sin estructura frágil.
- **Alternativas descartadas:** (a) crear un doc nuevo por día → fragmenta el diario y llena Drive (que ni siquiera está habilitado); (b) una hoja de cálculo con columnas → más fría y difícil de leer en el celular; (c) solo guardar en memoria del agente → no es durable ni consultable por HeC.

## 5. Cómo se nota mi configuración frente a un agente genérico

- El diario tiene un nombre y destino específicos definidos por HeC ("Diario de aprendizaje - HeC"), no un genérico "Notes".
- La fecha se calcula en **CDMX (UTC-6)**, no en la zona del servidor.
- La entrada y la confirmación van en **español mexicano, de tú, con 🐻**, tono corto (SOUL.md).
- Añadir una entrada al diario es seguro, reversible y de bajo riesgo (no modifica nada existente): por eso actúo primero (SOUL.md), y esto está respaldado por la **excepción de la regla 8** definida en AGENTS.md para la skill learning-journal. En cambio, **editar o borrar entradas anteriores, o compartir el Doc, sí requiere confirmación** (regla 8). Si falta algo, hago **una** pregunta (regla 14).

## 6. Servicios usados

- **Google Docs** (vía Zapier, ya conectado): buscar/crear el Doc y agregar texto.
- **Telegram** (canal ya activo): entrada y confirmación.
- **No requiere** APIs nuevas, OAuth nuevo, ni apps adicionales. **Confirmado:** Gmail, Drive y Tasks no se tocan.

## 7. Riesgos y casos límite

- **El Doc no existe:** se crea y se **avisa explícitamente** ("Creé el diario porque no existía").
- **El Doc existe con otro contenido:** solo se agrega al final; nunca se borra ni reescribe nada.
- **HeC dicta algo ambiguo o incompleto:** pido **una** aclaración puntual (regla 14), no adivino (regla 15).
- **HeC dicta datos sensibles:** se registran en el diario (es su doc privado), pero **nunca** se copian a archivos del repo, commits ni mensajes a terceros (regla 5).
- **Falla la conexión con Zapier:** lo reporto tal cual (regla 16), no finjo que se guardó; propongo reintentar.
- **Cuándo parar y preguntar:** si HeC pide editar o borrar entradas anteriores (regla 8: no edito/borro sin confirmación) o compartir el doc con alguien (regla 2: no comparto información personal; regla 8). Añadir una entrada nueva al final no requiere confirmación adicional: la excepción de la regla 8 (learning-journal) lo autoriza, al ser una acción reversible y de bajo riesgo.

## 8. Plan de prueba

- **Entrada real:** lo dicta HeC el día de la prueba (el ejemplo de la sección 2 es solo ilustrativo).
- **Resultado esperado:** entrada con la fecha del día en CDMX y las viñetas dictadas, agregada al final del Doc "Diario de aprendizaje - HeC" (creándolo si falta), y confirmación en Telegram.
- **Evidencia a capturar:** captura del final del Doc mostrando la entrada nueva con fecha correcta CDMX + el mensaje de confirmación de Bery.

---

# SKILL 2: smart-calendar-event

## 1. ¿Qué hace?

Convierte la descripción en lenguaje natural de un evento en una propuesta concreta de fecha y hora en zona CDMX (respetando el horario laboral 9:00-18:00) y, **solo tras la confirmación de HeC**, la crea en su calendario principal con Google Calendar vía Zapier.

## 2. ¿Qué entrada necesita?

**Formato:** un mensaje de Telegram describiendo el evento en lenguaje natural: qué, cuándo (relativo o absoluto), duración y extras como recordatorio.

**Ejemplo real:**
> "Sesión de estudio el jueves por la tarde, dos horas, con recordatorio."

**Qué sé ya por los archivos de configuración:**
- USER.md: zona horaria CDMX (UTC-6), horario laboral 9:00-18:00, trato de tú, 🐻.
- TOOLS.md: calendario principal de la cuenta Google conectada; no crear eventos sin preguntar primero.
- AGENTS.md: regla 9 — **no creo eventos sin aprobación explícita**; regla 14 — una pregunta a la vez si falta información.
- SOUL.md: propongo lo más razonable ante incertidumbre, sin inventar; si es ambiguo, pregunto.

## 3. ¿Cómo es un buen resultado?

**Formato de propuesta (antes de crear):** evento con título, fecha, hora de inicio y fin, y recordatorio, en español y claro para confirmar de un vistazo.

**Ejemplo de propuesta:**
> "¿Te confirmo este evento?
> 📅 Sesión de estudio
> 🗓️ Jueves 15 de octubre, 16:00–18:00 (CDMX)
> ⏰ Recordatorio 30 min antes
> ¿Lo creo?"

**Destino exacto (tras confirmación):** calendario principal de la cuenta Google conectada vía Zapier, con hora en CDMX (UTC-6).

**Criterios verificables de éxito:**
1. La propuesta muestra fecha/hora correctas en CDMX dentro de 9:00-18:00 (salvo que HeC pida otra cosa).
2. **Nada se crea antes del "sí" de HeC** (regla 9).
3. Tras confirmar, el evento aparece en el calendario con título, horario y recordatorio correctos.
4. Se confirma con el detalle del evento creado (fecha, hora, recordatorio) y su enlace si está disponible.

## 4. Por qué esta tarea, esta entrada y este formato

- **Por qué esta tarea:** HeC vive entre reuniones y sesiones propias; escribir "jueves por la tarde, 2h" es más rápido que abrir Calendar y llenar campos. Es exactamente el tipo de fricción que un asistente personal debe quitar.
- **Por qué esta entrada:** lenguaje natural es lo que HeC ya usa en Telegram; cero aprendizaje de formato.
- **Por qué este formato de resultado:** "proponer primero, crear después" es lo único compatible con AGENTS.md (regla 9) y evita eventos basura; el formato del mensaje de propuesta está pensado para leerse en una pantalla de celular.
- **Alternativas descartadas:** (a) crear el evento directo sin confirmar → viola AGENTS.md regla 9; (b) pedir un formulario completo de datos → mata la ventaja del lenguaje natural; (c) interpretar "tarde" como 12:00 o 19:00 a criterio del modelo → inventar (regla 15); la propuesta explícita resuelve la ambigüedad.

## 5. Cómo se nota mi configuración frente a un agente genérico

- Resuelve fechas en **CDMX** — no asume la zona del servidor — y **respeta la jornada 9:00-18:00** de USER.md (un agente genérico propondría 19:00 o usaría UTC).
- Pide confirmación porque AGENTS.md lo exige; un agente genérico crearía el evento sin avisar.
- Responde en español mexicano, de tú, con 🐻 y formato Telegram (SOUL.md).
- Usa el calendario principal de la cuenta que ya está conectada (TOOLS.md), sin pedir configuración de nada.

## 6. Servicios usados

- **Google Calendar** (vía Zapier, ya conectado): consultar disponibilidad y crear el evento tras confirmación.
- **Telegram** (canal ya activo): recibir la descripción, proponer y confirmar.
- **No requiere** APIs nuevas, OAuth nuevo, ni apps adicionales. **Confirmado:** Gmail, Drive y Tasks no se tocan.

## 7. Riesgos y casos límite

- **Día ambiguo ("el jueves"):** uso el jueves más próximo en CDMX y lo muestro con fecha explícita en la propuesta para que HeC verifique.
- **Fuera de horario laboral:** propongo dentro de 9:00-18:00; si HeC pide algo fuera (clase, cena), lo respeto sin juzgar — él manda.
- **Hora de fin pasada la jornada (16:00-19:00):** lo ajusto a 18:00 y lo menciono en la propuesta, o pregunto.
- **Choca con otro evento:** reviso disponibilidad y aviso: "Ya tienes X a esa hora; ¿lo muevo a las 17:00?" (una sola pregunta, regla 14).
- **Falta información esencial (qué, cuándo o duración):** pregunto **una** cosa a la vez; no invento.
- **Recordatorio:** uso 30 minutos por defecto solo si HeC no especifica, y lo digo en la propuesta.
- **Cuándo parar y preguntar:** siempre antes de crear (regla 9); también si HeC pide editar o borrar eventos existentes (regla 9) o moverlos a otro calendario.

## 8. Plan de prueba

- **Entrada real:** lo describe HeC el día de la prueba (el ejemplo de la sección 2 es solo ilustrativo).
- **Resultado esperado:** Bery propone fecha, hora y recordatorio en CDMX, dentro de 9:00-18:00 (o lo que HeC pida), y espera confirmación; tras el "sí", crea el evento en el calendario principal y confirma con el detalle.
- **Evidencia a capturar:** captura de la propuesta en Telegram (antes del "sí") + captura del evento en Google Calendar con fecha/hora correctas (después del "sí") + mensaje de confirmación de Bery.

---

## Qué dejé fuera y por qué

- **Gmail:** no está habilitado vía Zapier. Cuando lo esté (si HeC lo decide), se podrá diseñar una skill de bandeja/borradores, respetando la regla 7 (nunca enviar sin aprobación; máximo borrador).
- **Google Drive:** no está habilitado vía Zapier. Por eso el diario se crea directamente con Docs y no hay carpeta por defecto. La carpeta "HeC - Trabajo" queda como idea futura, no como configuración.
- **Google Tasks:** no está habilitado vía Zapier. La lista "Bandeja HeC" para pendientes de correos queda pendiente de que exista el servicio conectado; no se puede implementar hoy.
- **Skills de Zapier (create_zapier_skill):** descartadas a propósito — las skills de este proyecto son de OpenClaw: carpetas con su SKILL.md en el workspace, como se acordó. Las acciones de Zapier se invocan directamente desde las skills de OpenClaw cuando toque implementar.
- **Cualquier skill que requiera apps u OAuth nuevos:** fuera de alcance por regla 12 de AGENTS.md.