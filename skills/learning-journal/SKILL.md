---
name: learning-journal
description: Registra lo que HeC aprendió hoy como entrada fechada en el Google Doc 'Diario de aprendizaje - HeC' vía Zapier.
---

# Learning Journal

Registra en el diario de aprendizaje de HeC lo que dicta por Telegram. Entrada fechada en zona CDMX, agregada al final del Doc.

## Antes de actuar: lee la configuración (una vez por sesión si no cambió)

1. `USER.md` — zona horaria CDMX (UTC-6), idioma español, trato de tú, 🐻.
2. `SOUL.md` — actúo primero cuando es seguro y reversible; tono directo, cálido, sin falsa energía.
3. `AGENTS.md` — reglas de privacidad (1-6), frenos de mano (7-11), configuración (12-13), estilo (14-16).
4. `TOOLS.md` — defaults: cuenta Google principal conectada en Zapier; el diario se crea/edita con Docs; nunca repetir correos ni datos personales en mensajes.

## Cuándo se activa

HeC dice cosas como:
- "esto aprendí hoy: ..."
- "anota en mi diario: ..."
- "agrega al diario: ..."
- Cualquier dictado de puntos de aprendizaje del día.

## Flujo (orden estricto)

1. **Recibe el dictado** de HeC (puntos sueltos, viñetas, lista numerada, o prosa).
2. **Interpreta la fecha de hoy en CDMX (UTC-6)**. Usa la hora/fecha actual del sistema convertida a America/Mexico_City.
3. **Formatea la entrada** con el encabezado de fecha en español y viñetas limpias (ver plantilla). No inventes contenido: usa exactamente lo que dictó, solo ordenado y sin redundancia.
4. **Busca el Doc** con `google_docs_find_a_document` (acción `document`, parámetro `title` = `Diario de aprendizaje - HeC`, carpeta por defecto).
5. **Si NO existe:** créalo con `google_docs_create_document_from_text` (acción `newtxtdocument`, parámetros `title` = `Diario de aprendizaje - HeC`, `file` = la entrada formateada). Avísale a HeC que lo creaste.
6. **Si SÍ existe:** agrega la entrada al final con `google_docs_append_text_to_document` (acción `append`, parámetros `file` = el Doc encontrado, `text` = la entrada formateada, `newline` = true).
7. **Confirma en Telegram** (ver plantilla de confirmación).

**Antes de cada llamada a Zapier:** usa `inspect_zapier_actions` para confirmar el nombre exacto de la acción y sus parámetros. Nunca adivines nombres.

## Plantilla de entrada (output)

Formato exacto de lo que se agrega al Doc (la fecha y el día de la semana se calculan siempre en CDMX en el momento de usarse; nunca se copia un valor fijo):

```markdown
## <AAAA-MM-DD> — <Día de la semana>

- Primer aprendizaje del día.
- Segundo aprendizaje del día.
- Tercer aprendizaje del día.
```

**Ejemplo** (si HeC dicta: "aprendí a leer estados de flujo, y la diferencia entre GOP y EBITDA"):

```markdown
## <AAAA-MM-DD> — <Día de la semana>

- Leer estados de flujo de efectivo.
- Diferencia entre GOP y EBITDA.
```

## Plantilla de confirmación (Telegram)

```
Listo, agregado al diario 📝
[Diario de aprendizaje - HeC](<enlace al Doc>)
```

Si el Doc era nuevo, añade: "Creé el doc porque no existía."

## Casos límite

- **Input vacío o ambiguo:** haz **UNA sola pregunta** (regla 14). Ej.: "¿Qué aprendiste hoy?" Nunca inventes contenido.
- **Doc inexistente:** créalo (paso 5) y dilo explícitamente en la confirmación.
- **HeC pide editar o borrar entradas anteriores:** **detente y pide confirmación** (regla 8: la excepción solo cubre AÑADIR al final).
- **HeC pide compartir el Doc:** **detente** (regla 2 y 8). Nunca compartas sin aprobación.
- **HeC dicta datos sensibles:** se registran en su Doc privado, pero **nunca** se copian a archivos del repo, commits ni mensajes a terceros (regla 5).
- **Falla la conexión con Zapier:** repórtalo tal cual (regla 16), no finjas que se guardó; propón reintentar.

## Verificación final

1. El Doc existe (creado o ya existente) con el nombre exacto.
2. La entrada tiene la fecha de hoy en CDMX.
3. Los puntos dictados están todos, sin inventar.
4. Se envió confirmación con enlace al Doc.
5. No se modificó ni borró nada más del documento.