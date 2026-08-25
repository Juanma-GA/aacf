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

# Plantilla de Herramienta Interna

## Pila tecnológica
- Backend: Python 3.12 + FastAPI (o CLI con Click/Typer)
- Frontend (si se necesita UI): React 18 + Vite
- Autenticación: Keycloak SSO
- Despliegue: Contenedor Docker en infraestructura compartida

## Estructura del proyecto
```
tool-name/
├── app/
│   ├── core/           # Core tool logic
│   ├── api/            # API endpoints (if web-based)
│   ├── cli/            # CLI commands (if CLI-based)
│   ├── config.py       # Configuration
│   └── main.py         # Entry point
├── tests/
├── pyproject.toml
├── Dockerfile
└── README.md
```

## Requisitos
- Debe estar registrada en el Registro de Herramientas de IA antes del despliegue
- Evaluación de riesgo completada (detectada automáticamente a partir de las capacidades)
- Formación completada por todos los usuarios previstos
- Revisión de IS para herramientas de riesgo elevado/alto

## Ruta de despliegue
- T1: Solo máquina local del desarrollador
- T2: VM compartida (TI aprovisiona, IS despliega)
- T3: VM dedicada con acceso al almacén de datos
- T4: Infraestructura de producción (IS + TI conjunto)
</content>
