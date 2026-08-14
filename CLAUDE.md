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

## GitHub y GitLab

Este repo vive en **los dos**, con la convencion de nombres de la flota:

| Remote | Plataforma | URL |
|---|---|---|
| `github` | GitHub | `git@github.com:abelardodiaz/web26-101-ycloud-mcp.git` |
| `origin` | GitLab | `git@gitlab.com:abelardodiaz/web26-101-ycloud-mcp.git` |

**Ojo con `origin`:** en esta flota `origin` es **GitLab**, no GitHub. El 091 lo hizo al reves
y por eso hay que decirlo. Verifica con `git remote -v` antes de asumir.

### Push: siempre a los dos

```bash
git push github main && git push origin main
```

No es opcional. Un repo pusheado a uno solo se desincroniza en silencio y luego nadie sabe
cual va adelante. Si uno de los dos falla, **no des el trabajo por subido**: arregla y repite.

### Reparto de roles

- **GitHub = cara publica.** Es la URL que se comparte, la que va en `PROJECT.yaml`, la que
  se registra si el servidor se publica en el registry de MCP o en awesome-mcp-servers, y
  donde llegarian issues y PRs de terceros.
- **GitLab = espejo de respaldo.** Misma rama `main`, mismo contenido. Existe para que el
  codigo no dependa de una sola cuenta.

### Antes de cada push, en este repo mas que en ninguno

Este proyecto toca WhatsApp real. Un push es irreversible: aunque borres el commit despues,
el contenido ya salio. Revisa el diff **antes**, no despues:

```bash
git diff --cached
```

Nunca deben salir: la API key de YCloud, numeros de telefono reales, contenido de mensajes,
`mensajes.jsonl`, logs, ni el hostname del tunel de 851. El `.gitignore` cubre los casos
conocidos; el que se te ocurra a ti no lo cubre nadie.

### Ramas y CI

- Rama unica `main`. Si el proyecto crece a ramas de trabajo, se abre PR en **GitHub** y
  GitLab sigue siendo espejo.
- No hay CI configurado. Si se agrega, va en GitHub Actions; GitLab se queda sin pipeline
  para no correr todo dos veces.

### Herramientas

`gh` y `glab` estan autenticados **en WSL**, no en Windows. Desde una sesion en Windows se
usan con `wsl bash -lc "..."` en una sola capa — anidar mas se come la sustitucion de
comandos (leccion conocida de la flota).

Aviso: `glab auth status` puede reportar `Invalid token provided` con un token perfectamente
valido. Antes de concluir que el acceso a GitLab esta roto, comprueba con `glab api user`.

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
