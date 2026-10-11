# DEFENSA.md — Por qué diseñé así mis skills

## Resumen
Usé mi agente Bery (OpenClaw) ya funcionando. Configuré los 5 archivos (IDENTITY, SOUL, AGENTS, USER, TOOLS) con contenido específico mío, escribí SKILLS_DESIGN.md **antes** de implementar (commit d3f8523, anterior a las skills 413b0d3 y ff977fa) e implementé 2 skills con Google Docs y Google Calendar vía Zapier, sin agregar APIs ni servicios nuevos.

## Por qué estas dos skills
Elegí tareas que hago a diario y que tienen un resultado verificable en un servicio real:
- **learning-journal:** hoy mis aprendizajes se pierden. Un dictado por Telegram queda como entrada fechada en un Doc.
- **smart-calendar-event:** convertir "sesión de estudio el jueves por la tarde" en un evento exacto cuesta fricción. La skill propone y yo apruebo.
Las dos usan servicios que ya estaban conectados (Docs y Calendar), así que cumplen la regla de no añadir APIs.

## Por qué skill y no un prompt suelto
Una skill (carpeta + SKILL.md) hace repetible el flujo: orden estricto de pasos, formato fijo de salida, casos límite y verificación final. Con un prompt suelto, el resultado cambiaría cada vez.

## learning-journal
- **Decisión clave:** mi dictado cuenta como aprobación para AÑADIR al final del Doc (excepción documentada en la regla 8 de AGENTS.md). Editar, borrar o compartir sigue pidiendo confirmación. Así actúa rápido en lo reversible y se frena en lo delicado.
- **Riesgo:** guardar datos sensibles. Mitigación: reglas de privacidad 1-6, el contenido solo va a mi Doc, nunca al repo ni a mensajes a terceros.
- **Evidencia:** docs/EVIDENCIA.md (Doc creado en la primera prueba, append en la segunda, fecha correcta en CDMX).
- **Limitación conocida:** con dos dictados el mismo día, el encabezado de fecha se repite, porque append solo agrega al final. Lo dejé así a propósito para no leer ni modificar el Doc existente. Una mejora futura sería detectar la entrada del día.

## smart-calendar-event
- **Decisión clave:** propone y espera mi "sí" antes de crear (regla 9). Un evento mal creado ensucia mi calendario y avisa a otros, por eso aquí NO actúa primero.
- **Interpretación:** "tarde" se resuelve como 16:00-18:00 por mi jornada de 9:00-18:00 (USER.md) y el recordatorio por defecto es de 30 minutos. Fechas siempre en CDMX.
- **Evidencia:** la propuesta salió antes de crear nada; tras mi confirmación se creó el jueves 15, 16:00-18:00, con recordatorio de 30 minutos.
- **Riesgo:** fecha mal interpretada. Mitigación: la propuesta siempre muestra la fecha explícita para que yo la verifique.

## Cómo se reflejan los 5 archivos
- IDENTITY.md y SOUL.md: nombre, tono directo y cálido, español de México, de tú, 🐻; actúa primero en lo reversible.
- AGENTS.md: reglas de privacidad y "frenos de mano" (nada de enviar correos, borrar o crear eventos sin mi aprobación).
- USER.md: zona CDMX, jornada 9:00-18:00, mis objetivos. La versión pública está generalizada; los datos reales viven en USER.private.md, que está en .gitignore.
- TOOLS.md: Google Docs y Calendar por Zapier; Gmail, Drive y Tasks no están habilitados.

## Qué dejé fuera y por qué
Gmail (enviar correos es irreversible y no está habilitado en Zapier), Drive y Tasks (no habilitados) y cualquier skill que requiera una API o OAuth nuevo (prohibido por el proyecto).

## Problemas reales y cómo los resolví
- La autorización de Zapier se había caído y el callback de OAuth apuntaba a 127.0.0.1 en un servidor sin navegador. Solución: copié el código del navegador y lo entregué desde el servidor.
- La clave del proveedor del modelo había expirado. Solución: renovarla y comprobar que tenga el formato correcto.
- Privacidad: no quería datos personales en GitHub. Separé USER.md (público, generalizado) de USER.private.md (local), saqué memory/ del índice, reescribí el historial local antes de publicar y usé el correo noreply de GitHub en los commits.

## Preguntas probables
- **¿Por qué dos skills con servicios distintos?** Cubren dos formas de actuar: una registra de forma reversible (diario) y otra actúa sobre mi agenda, por lo que exige aprobación.
- **¿Qué pasa si falla Zapier?** La skill dice que se reporte el fallo tal cual y no se finja éxito (regla 16).
- **¿Cómo evité exponer información personal?** Con la separación USER.md / USER.private.md, memory/ ignorado y la revisión de historial con git grep y git log -S antes del push.
- **¿Qué mejoraría?** Detectar la entrada del día en el diario, agregar una prueba de freno dedicada y migrar los secretos de openclaw.json a SecretRefs.