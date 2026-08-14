# CLAUDE.md - web26-101 YCloud MCP

Eres **claude-101**, agente de este proyecto.

## Que es esto

Servidor MCP para la API v2 de **YCloud**: WhatsApp Business, SMS y email en un solo
servidor, con las herramientas prefijadas por canal (`whatsapp_*`, `sms_*`, `email_*`). Es
un proveedor, una API key, un modelo de auth — por eso no se parte en tres.

## No empiezas de cero

`C:\Users\abela\prweb\web26-851-opencode-ycloud` (privado, v0.2.0, **vivo**) ya opera contra
la API real y ya resolvio lo caro: validacion de firma `YCloud-Signature`, eventos `inbound`
/ `smb.history` / `message.updated`, y **descarga de multimedia con reintento**.

Tu trabajo es **extraer ese cliente**, no reescribirlo. Empieza por `run.py` y
`descargar_media.py` de 851.

| Se queda en 851 | Viene aqui |
|---|---|
| receptor de webhooks, tablero web, auth JWT | cliente de la API v2 |
| `mensajes.jsonl`, `.env`, logs, tunel | validacion de firma |
| transcripcion whisper | descarga de media con reintento |

**851 es un proyecto vivo.** Avisa por memo en su `docs/incoming/` antes de extraer, y no
edites sus archivos.

## Reglas

- **Repo publico.** JAMAS: API key de YCloud, numero de WhatsApp real, historiales de
  mensajes, logs, hostname del tunel. Placeholders siempre. Revisa cada diff.
- **Sin emojis** en scripts bash y python: se corren desde PowerShell en Windows.
- **Ruff obligatorio**: `[tool.ruff]` en `pyproject.toml` + `ruff>=0.8` en dev deps.
- **PROJECT.yaml en cada commit**: `version`, `updated_at`, `updated_by: claude-101`.
- **Dos remotes**: GitHub y GitLab, se pushea a ambos.

## Comunicacion entre proyectos

`docs/incoming/` es la bandeja de memos de otros proyectos de la flota. Al incorporar uno,
**muevelo a `docs/procesados/`** — no lo borres. Para escribir a otro proyecto, skill
`reportar-a-proyecto`; nunca edites codigo ni docs ajenos.

## Decisiones abiertas

1. **Webhooks.** Un MCP stdio no puede recibir POSTs. Opciones: (a) solo lectura por polling
   de la API, (b) leer del almacen que 851 ya llena, (c) modo http con endpoint propio. La
   (a) deja este repo autonomo y sin infraestructura. Registra la decision como ADR.
2. **SMS y email**: confirmar contra la API real (no contra la doc) si van con la misma key
   que WhatsApp o requieren alta aparte. Leccion de la flota: no medir a traves del
   instrumento equivocado.
3. Licencia (el repo aun no tiene una).
