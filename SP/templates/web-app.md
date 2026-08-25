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

# Plantilla de Aplicación Web

## Pila tecnológica
- Frontend: React 18 + TypeScript + Vite 6
- Estilos: Tailwind CSS 4 + shadcn/ui
- Estado: Zustand
- Backend: FastAPI + SQLAlchemy 2.0 async
- Base de datos: PostgreSQL 16
- Autenticación: Keycloak OIDC

## Estructura del proyecto
```
project-name/
├── frontend/
│   ├── src/
│   │   ├── components/     # Reusable UI components
│   │   ├── pages/          # Page-level components
│   │   ├── services/       # API client layer
│   │   ├── stores/         # Zustand stores
│   │   ├── hooks/          # Custom React hooks
│   │   └── types/          # TypeScript types
│   ├── package.json
│   ├── vite.config.ts
│   └── tailwind.config.ts
├── backend/
│   ├── app/
│   │   ├── api/            # Route handlers
│   │   ├── db/             # Models + session
│   │   ├── services/       # Business logic
│   │   ├── auth/           # OIDC integration
│   │   ├── config.py       # Settings
│   │   └── main.py         # FastAPI app
│   ├── pyproject.toml
│   └── Dockerfile
├── docker-compose.yml
└── README.md
```

## Checklist de seguridad
- [ ] Autenticación OIDC configurada
- [ ] CORS restringido a orígenes conocidos
- [ ] Validación de entrada en todos los endpoints
- [ ] Prevención de inyección SQL (consultas parametrizadas)
- [ ] Prevención de XSS (escapado por defecto de React)
- [ ] Protección CSRF
- [ ] Limitación de tasa en endpoints sensibles
- [ ] Secretos en variables de entorno, no en el código
</content>
