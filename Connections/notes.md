# Notas de configuración - Conectar OpenClaw (Telegram + Zapier MCP)

- Se creó el bot de Telegram con BotFather y se agregó como canal en OpenClaw con `openclaw channels add`. El emparejamiento (`openclaw pairing approve`) fue directo y sin problemas.
- Al configurar el MCP de Zapier, el primer intento de registro (`mcporter config add zapier`) no se guardó correctamente la primera vez; `mcporter list` mostraba "No MCP servers configured". Se resolvió repitiendo el comando directamente en la terminal.
- El mayor obstáculo fue la autenticación OAuth: el código de autorización que Zapier genera caduca en pocos segundos, y el callback redirige a `127.0.0.1` del propio VPS (no accesible desde el navegador local), por lo que Safari siempre mostraba "no se puede conectar".
- La solución fue abrir una segunda sesión SSH al VPS y, dentro de esa terminal, ejecutar manualmente `curl` contra la URL de callback fallida (código + state) apenas se generaba, simulando el redirect desde el propio servidor. Esto canjeó el código por el token exitosamente.
- Al conectar Google Docs, la primera autorización se quedó con permisos insuficientes (solo lectura); se resolvió reconectando la cuenta y autorizando de nuevo con permisos de escritura.
- Con Zapier MCP ya autenticado, el agente (Bery) pudo crear un documento en Google Docs y un evento en Google Calendar en una sola conversación por Telegram, confirmando el flujo de punta a punta.
