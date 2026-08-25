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

# Plantilla de Pipeline de Datos

## Pila tecnológica
- Lenguaje: Python 3.12
- Orquestación: Tareas programadas (cron / planificador de la consola de administración)
- Datos: pandas / polars para procesamiento, asyncpg para acceso a BD
- Almacenamiento: Almacén de datos PostgreSQL, exportaciones de ficheros a unidad compartida

## Estructura del proyecto
```
pipeline-name/
├── src/
│   ├── extract/        # Data source connectors
│   ├── transform/      # Processing logic
│   ├── load/           # Destination writers
│   ├── utils/          # Shared utilities
│   ├── config.py       # Configuration
│   └── main.py         # Pipeline entry point
├── tests/
├── pyproject.toml
├── Dockerfile
└── README.md
```

## Principios de diseño
- Ejecución idempotente (seguro volver a ejecutar)
- Puntos de control (checkpointing) para pipelines de larga duración
- Registro estructurado con IDs de correlación
- Manejo de errores con reintento + cola de mensajes fallidos (dead-letter)
- Validación de datos en los límites (entrada/salida)
- Consciente de la clasificación: respeta los niveles de clasificación de datos

## Reglas de clasificación de datos
- PÚBLICO: Sin restricciones de procesamiento
- INTERNO: Registra el acceso, restringe los destinos de salida
- CONFIDENCIAL: Cifra en reposo, audita todo acceso
- ESTRICTAMENTE_CONFIDENCIAL: Procesamiento aislado, sin exportación masiva
</content>
