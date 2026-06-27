# CLAUDE.md — Agenda Ameca

Contexto para Claude Code al trabajar en este repositorio.

## Qué es

Asistente conversacional (n8n) para gestionar la agenda de la Presidenta
Municipal de Ameca vía Google Calendar (`amecaayuntamiento@gmail.com`).
Lo usan 5 personas: Presidenta, Chelita, Iván, Marleth, Yolanda.

Arquitectura completa en [`docs/arquitectura.md`](docs/arquitectura.md). Resumen:
dos workflows n8n que hablan por MCP — **MCP CALENDAR** (server MCP con 6 tools
de Google Calendar) y **Agente Agenda Personal** (AI Agent con Ollama + memoria
Postgres + MCP Client, canal **Telegram**).

## Estructura del repo

- `workflows/` — los 2 workflows como JSON (export de la API de n8n, sin secretos).
- `infra/` — `docker-compose.yml` de referencia y `mcp-setup.md` (conectar n8n a Claude Code).
- `docs/` — arquitectura, credenciales a recrear, gotchas.
- `.env.example` — plantilla de secretos (el `.env` real no se versiona).

## Reglas de trabajo

- **No tocar los workflows en la instancia n8n sin que el usuario lo pida.**
- Al editar workflows, respeta los gotchas de [`docs/gotchas.md`](docs/gotchas.md)
  (sobre todo `additionalFields` + `$fromAI` en googleCalendarTool).
- Los secretos (API key de n8n, `N8N_ENCRYPTION_KEY`, tokens OAuth) **nunca**
  se commitean. Ver `.gitignore`.
- Idioma de interacción: español.

## Recrear desde cero

Procedimiento paso a paso en [`README.md`](README.md).
