# Asistente Agenda

Asistente conversacional en **n8n** para gestionar la agenda de la Presidencia
Municipal de Ameca a través de Google Calendar. Este repo contiene **todo lo
versionable** del proyecto para poder recrearlo en cualquier server.

> El código (workflows + infra + docs) vive aquí. Los **secretos y credenciales
> no** — se recrean a mano (ver [`docs/credenciales.md`](docs/credenciales.md)).

## Contenido

```
agenda-ameca/
├── README.md                  # este archivo
├── CLAUDE.md                  # contexto para Claude Code
├── .env.example               # plantilla de secretos
├── .gitignore
├── workflows/
│   ├── mcp-calendar.json             # server MCP (6 tools Google Calendar)
│   └── agente-agenda-personal.json   # AI Agent (Ollama + Postgres + MCP + Telegram)
├── infra/
│   ├── docker-compose.yml     # stack n8n + postgres de referencia
│   └── mcp-setup.md           # conectar n8n a Claude Code vía MCP
└── docs/
    ├── arquitectura.md
    ├── credenciales.md
    └── gotchas.md
```


## Recrear el proyecto desde cero

1. **Clonar** el repo y preparar entorno:
   ```bash
   git clone <url> agenda-ameca && cd agenda-ameca
   cp .env.example .env   # rellena los valores
   ```
2. **Levantar n8n + postgres** (o usar EasyPanel como el original):
   ```bash
   docker compose -f infra/docker-compose.yml --env-file .env up -d
   ```
3. **Crear las 4 credenciales** en la UI de n8n: Google Calendar OAuth2,
   Telegram, Ollama y Postgres. Detalle en [`docs/credenciales.md`](docs/credenciales.md).
4. **Importar los workflows** (UI de n8n → Import from File, o vía API):
   ```bash
   curl -s -X POST -H "X-N8N-API-KEY: $N8N_API_KEY" \
     -H "Content-Type: application/json" \
     --data @workflows/mcp-calendar.json \
     "$N8N_API_URL/api/v1/workflows"
   ```
   Repite con `agente-agenda-personal.json`.
5. **Re-vincular credenciales** en cada nodo (los IDs de credencial cambian) y
   ajustar el path del MCP en el nodo *MCP Client* (ver gotcha #4).
6. **Provisionar Ollama** con el modelo `gemma4:31b` (verifica el tag) y apuntar
   la credencial *Ollama account* a su URL.
7. **Activar** ambos workflows y probar desde Telegram.
8. (Opcional) **Conectar a Claude Code** siguiendo [`infra/mcp-setup.md`](infra/mcp-setup.md).

## Liberar el server actual

Subir este repo **no libera el server por sí solo**: el stack n8n sigue
corriendo en Docker (EasyPanel). Para liberarlo de verdad, una vez confirmado
que el repo está completo y respaldado en GitHub, hay que tumbar el stack
(`n8n_n8n`, `n8n_n8n-db`, y opcionalmente EasyPanel/Traefik). Eso es
**destructivo**: hazlo solo tras verificar el backup.
