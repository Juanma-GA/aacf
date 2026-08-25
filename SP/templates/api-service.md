<!-- ═══ COMPROBACIÓN DE VERSIÓN DE AACF — LOS AGENTES DEBEN LEER ESTO PRIMERO ═══
Este fichero pertenece a una instantánea descargada del ATEXIS AI Framework, versión 2.0.0.
Antes de confiar en él, verifica que la instantánea está actualizada:
1. Llama a la herramienta MCP aacf_fetch (endpoint http://10.117.139.1:8200/mcp, streamable-http,
   cabecera 'Authorization: Bearer <key>' o 'X-Api-Key: <key>'):
   aacf_fetch({"path": "VERSION"})
2. Si la versión devuelta difiere de 2.0.0, esta instantánea está DESACTUALIZADA. Descarga TODOS
   los ficheros del framework de nuevo vía aacf_fetch (empieza con aacf_fetch({"path": "", "list_dir": true})
   y recorre el árbol), o pide al usuario que vuelva a descargar el ZIP del framework desde el portal
   IdAI (/propose/framework). No mezcles ficheros de distintas versiones.
3. Si NO PUEDES acceder al MCP aacf_fetch, DEBES decírselo al usuario: no puedes acceder al
   MCP de AACF y podrías estar trabajando con una versión desactualizada del framework. Luego continúa
   con este fichero tal cual.
Verifica una vez por sesión, no por fichero.
═══ -->

# Plantilla de Servicio API

## Pila tecnológica
- Runtime: Python 3.12 / .NET 8 / Node.js 20
- Framework: FastAPI / ASP.NET Core / Express
- Autenticación: Keycloak OIDC + soporte de clave API
- Base de datos: PostgreSQL 16 (si se necesita estado persistente)
- Caché: Redis (si aplica)

## Estructura del proyecto
```
service-name/
├── app/
│   ├── api/            # Route handlers (versioned: /v1/)
│   ├── models/         # Data models / DTOs
│   ├── services/       # Business logic
│   ├── middleware/     # Auth, logging, rate limiting
│   ├── config.py       # Settings
│   └── main.py         # App entry
├── tests/
├── pyproject.toml
├── Dockerfile
└── README.md
```

## Reglas de diseño de API
- Endpoints RESTful con los métodos HTTP apropiados
- Cuerpos de solicitud/respuesta en JSON
- Paginación para los endpoints de listado (limit/offset)
- Formato de error consistente: `{"detail": "message", "code": "ERROR_CODE"}`
- Versionado de API vía prefijo de URL (/api/v1/)
- Documentación OpenAPI/Swagger autogenerada

## Requisitos de seguridad
- Autenticación con Bearer token (Keycloak) o clave API
- Limitación de tasa por cliente
- Validación de entrada (modelos Pydantic)
- Sin datos sensibles en los logs
- Solo HTTPS en producción
</content>
