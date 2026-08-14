# ycloud-mcp

Servidor MCP para la **API v2 de YCloud**, cubriendo sus tres canales: **WhatsApp Business,
SMS y email**.

> **Estado: andamiaje.** Todavia sin implementacion. Ver [Estado](#estado).

## Por que existe

YCloud expone WhatsApp Business (incluida la coexistencia con la app nativa sobre el mismo
numero), SMS y email detras de una sola API. Este servidor la traduce a herramientas MCP para
que un agente pueda leer conversaciones, bajar multimedia y enviar mensajes sin salir de su
cliente.

La parte que suele complicarse no es enviar texto: es la **descarga de multimedia con
reintento** y la **validacion de la firma `YCloud-Signature`**. Ambas se traen de una
implementacion privada que ya opera contra la API real.

## Herramientas previstas

Prefijadas por canal, en un solo servidor (un proveedor, una API key, un modelo de auth):

| Prefijo | Alcance |
|---|---|
| `whatsapp_*` | conversaciones, mensajes, multimedia, envio |
| `sms_*` | envio y estado |
| `email_*` | envio y estado |

## Estado

| Pieza | Estado |
|---|---|
| Cliente de API | por extraer de una implementacion privada existente |
| Herramientas MCP | pendiente |
| Empaquetado (`pyproject.toml`) | pendiente |
| Estrategia de webhooks | decision abierta (polling vs modo http) |

## Seguridad

Repo publico, **sin secretos**. No lleva API key de YCloud, ni numeros de WhatsApp reales,
ni historiales de mensajes, ni hostnames de tuneles. Todo eso queda fuera de git y bloqueado
por `.gitignore`.

## Licencia

Pendiente de definir.
