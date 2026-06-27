# Gotchas conocidos

## 1. googleCalendarTool: título/descripción/color viven en `additionalFields`

En el nodo `n8n-nodes-base.googleCalendarTool` (operación `create`):
- El **título del evento es `summary`**, y `description` y `color` también van
  **dentro de `additionalFields`** (no son campos de primer nivel).
- En `update` están dentro de `updateFields`.

Si `additionalFields`/`updateFields` están vacíos al usar el nodo como
herramienta AI, el LLM **no tiene parámetros donde escribir** → los eventos
salen sin título, sin descripción y sin color, **por más que el system prompt
lo ordene**. No es problema del prompt, es de la herramienta.

**Solución:** poblar esos campos con expresiones `$fromAI`, p. ej.:

```
summary: "={{ $fromAI('Title', 'Título del evento', 'string') }}"
```

Para disponibilidad (`resource=calendar`) los campos son `timeMin`/`timeMax`
de primer nivel.

## 2. Colores de Google Calendar

`color` / `colorId` para estatus de asistencia:

| colorId | Color | Uso sugerido |
|---------|-------|--------------|
| `10` | Verde (Basil) | confirmado / asiste |
| `5`  | Amarillo (Banana) | tentativo |
| `11` | Rojo (Tomato) | cancelado / no asiste |

## 3. Modelo Ollama pequeño y tool-calling

El modelo `gemma4:31b` (verifica el tag con `ollama list`) puede rendir mal
llamando herramientas. Si el agente no invoca bien el MCP, considera un modelo
con mejor soporte de function-calling.

## 4. webhookId del MCP cambia al reimportar

El `MCP Server Trigger` tiene un `webhookId`/path fijo
(`48d4c308-...`). Al importar el workflow en una instancia nueva, ese path
puede regenerarse: actualiza la URL en el nodo **MCP Client** del agente para
que apunte al server correcto.
