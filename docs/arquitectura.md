# Arquitectura

Asistente conversacional para gestionar la agenda de la Presidenta Municipal de
Ameca vía Google Calendar (cuenta `amecaayuntamiento@gmail.com`).
Lo administran 5 personas: Presidenta, Chelita, Iván, Marleth, Yolanda.

Son **dos workflows de n8n** que se comunican por MCP.

## 1. MCP CALENDAR  (`workflows/mcp-calendar.json`)

Es un **servidor MCP**. Un `mcpTrigger` expone 6 herramientas de Google Calendar
para que las consuma el agente:

| Nodo | Operación |
|------|-----------|
| MCP Server Trigger (`@n8n/n8n-nodes-langchain.mcpTrigger`) | expone el endpoint MCP |
| Create an event | crear evento |
| Get availability | disponibilidad (`timeMin`/`timeMax`) |
| Get an event | leer un evento |
| Get many events | listar eventos |
| Update an event | actualizar |
| Delete an event | borrar |

- Path del MCP server: `/mcp/48d4c308-c927-4b0d-8059-e8a787e180f8`
  (el `webhookId` se regenera al reimportar; actualiza el MCP Client del agente).
- Todos los nodos `googleCalendarTool` usan la credencial **Google Calendar account**.

## 2. Agente Agenda Personal  (`workflows/agente-agenda-personal.json`)

El **cerebro**. 6 nodos:

| Nodo | Rol |
|------|-----|
| Telegram Trigger | entrada de mensajes (canal: **Telegram**) |
| AI Agent1 (`@n8n/n8n-nodes-langchain.agent`) | orquesta razonamiento + herramientas |
| Ollama Chat Model (`lmChatOllama`) | LLM, modelo `gemma4:31b` |
| MCP Client (`mcpClientTool`) | consume el server MCP CALENDAR |
| Postgres Chat Memory1 | memoria conversacional persistente |
| Send a text message (Telegram) | respuesta al usuario |

## Estado (jun-2026)

- El canal quedó definido en **Telegram** (antes se evaluó WhatsApp/Evolution API).
  Credencial Telegram: **AYUNTAMIENTO 01**.
- Memoria conversacional en **Postgres** (ya no Simple Memory).
- Ambos workflows están **activos**.

## Flujo

```
Usuario ──Telegram──> Telegram Trigger ──> AI Agent1
                                            │  ├─ Ollama Chat Model (gemma4:31b)
                                            │  ├─ Postgres Chat Memory
                                            │  └─ MCP Client ──MCP──> MCP CALENDAR ──> Google Calendar
                                            └──> Send a text message (Telegram) ──> Usuario
```

## Colores de estatus en Google Calendar

Se usan `colorId` para marcar asistencia (ver `gotchas.md`):
`10` = verde (Basil), `5` = amarillo (Banana), `11` = rojo (Tomato).
