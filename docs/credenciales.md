# Credenciales a recrear

Los workflows exportados **referencian** credenciales por nombre, pero **NO
contienen los secretos** (n8n los cifra en su DB con `N8N_ENCRYPTION_KEY`).
Al reimportar en una instancia nueva, hay que crear estas 4 credenciales a mano
y re-vincularlas en los nodos.

| Credencial (nombre original) | Tipo n8n | Dónde se usa | Cómo recrearla |
|------------------------------|----------|--------------|----------------|
| **Google Calendar account** | `googleCalendarOAuth2Api` | MCP CALENDAR (6 nodos) | Flujo OAuth2 de Google con la cuenta `amecaayuntamiento@gmail.com`. Requiere proyecto en Google Cloud con la Calendar API y el OAuth client (ID/secret) → autorizar en n8n |
| **AYUNTAMIENTO 01** | `telegramApi` | Agente (Trigger + Send message) | Token del bot de Telegram (BotFather). Reconfigurar webhook |
| **Ollama account** | `ollamaApi` | Agente (Ollama Chat Model) | URL del servidor Ollama (ej. `http://host:11434`). Ver nota abajo |
| **Postgres account** | `postgres` | Agente (Postgres Chat Memory) | Host/puerto/db/usuario/password del postgres (mismo stack del compose) |

## ⚠️ Nota sobre Ollama

En el server original Ollama **NO está instalado localmente**
(`ollama: command not found`). El nodo usa el modelo `gemma4:31b` y la URL del
servidor vive dentro de la credencial *Ollama account*. Al reinstalar debes
decidir dónde corre Ollama (mismo host, otro contenedor, o remoto) y descargar
el modelo (`ollama pull gemma4:31b` — **verifica el tag**, modelos pequeños
locales rinden mal en tool-calling).

## Reimportar credenciales desde un backup (opcional)

Solo funciona si conservas el **`N8N_ENCRYPTION_KEY` original** y un dump del
postgres de n8n. Con eso, n8n descifra las credenciales tal cual. Si no tienes
la llave, no hay forma de recuperar los secretos: hay que recrearlos a mano
(camino elegido en este proyecto).
