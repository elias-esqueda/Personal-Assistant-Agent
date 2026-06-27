# Conectar n8n a Claude Code vía MCP

Permite que Claude Code audite, corrija y mejore los workflows **a petición** (nada automático).

## Servidor MCP usado

- Imagen community open-source: `ghcr.io/czlonkowski/n8n-mcp:latest` (modo stdio).
- Se conecta a la API pública de n8n con una API key.

## Registro (scope user)

```bash
claude mcp add n8n -s user -- \
  docker run -i --rm \
    -e MCP_MODE=stdio \
    -e LOG_LEVEL=error \
    -e DISABLE_CONSOLE_OUTPUT=true \
    -e N8N_API_URL="$N8N_API_URL" \
    -e N8N_API_KEY="$N8N_API_KEY" \
    ghcr.io/czlonkowski/n8n-mcp:latest
```

> Sustituye `$N8N_API_URL` y `$N8N_API_KEY` por valores reales (ver `.env`).
> La API key se genera en **n8n → Settings → n8n API**.

## Notas

- El MCP **no se carga en la sesión donde se registra**: reinicia Claude Code.
- Verifica con `claude mcp list` (debe salir `n8n … ✔ Connected`).
- La key queda guardada en `~/.claude.json` (NO subir ese archivo a git).
- Cuando la key expire, genera una nueva y vuelve a correr `claude mcp add n8n -s user`.

## Exportar workflows sin el MCP (vía API REST)

```bash
curl -s -H "X-N8N-API-KEY: $N8N_API_KEY" \
  "$N8N_API_URL/api/v1/workflows/<ID>" | jq '.' > workflows/<nombre>.json
```

IDs originales: `MCP CALENDAR` = `zmadYyT39TAUEqBb`, `Agente Agenda Personal` = `8DpW7ZKmIPsZL0ER`
(los IDs cambian al reimportar en una instancia nueva).
