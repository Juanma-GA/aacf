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

# Plantilla de Script de Automatización

## Pila tecnológica
- Lenguaje: Python 3.12 / PowerShell 7 / Bash
- Programación: Tareas programadas de la consola de administración
- Registro: JSON estructurado a stdout (capturado por el planificador)

## Estructura del proyecto
```
script-name/
├── src/
│   ├── main.py         # Entry point
│   ├── config.py       # Configuration from env
│   └── utils.py        # Shared utilities
├── tests/
├── pyproject.toml      # (if Python)
└── README.md
```

## Directrices
- Responsabilidad única: un script = una tarea
- Idempotente: seguro para volver a ejecutar sin efectos secundarios
- Códigos de salida: 0 = éxito, 1 = error, 2 = advertencia
- Salida estructurada: JSON a stdout para el procesamiento posterior
- Sin credenciales hardcodeadas: usa variables de entorno o cuentas de servicio de Keycloak
- Tiempo de espera: define el tiempo máximo de ejecución en la configuración del planificador
- Alertas: emite eventos de error que disparan alertas en la consola de administración

## Aprobación
- T1: Autoservicio (solo local)
- T2+: Requiere revisión de IS antes de programar
</content>
