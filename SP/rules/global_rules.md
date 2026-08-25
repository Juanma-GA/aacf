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

# Reglas Globales

Estas reglas aplican a TODA generación de código asistida por IA sin importar el lenguaje, el
framework, o el nivel.

## Regla 1: Seguridad primero
Nunca generes código que introduzca vulnerabilidades de seguridad. Aplica conciencia del OWASP Top 10
en todo momento. Valida las entradas, escapa las salidas, usa consultas parametrizadas.

## Regla 2: Sin secretos hardcodeados
Nunca hardcodees contraseñas, claves API, tokens, o cadenas de conexión. Usa siempre variables de
entorno o un gestor de secretos.

## Regla 3: Conciencia de la clasificación de datos
Todo código que maneje datos debe respetar los niveles de clasificación de datos de la organización
(Público, Interno, Confidencial, Estrictamente Confidencial). Nunca proceses o muestres datos por
encima del techo de clasificación del proyecto.

## Regla 4: Rastro de auditoría
Todas las operaciones significativas deben registrarse con contexto suficiente para la auditoría
(quién, qué, cuándo, dónde). Nunca registres datos sensibles (contraseñas, tokens, PII).

## Regla 5: Manejo de errores
Maneja los errores con elegancia. Nunca expongas trazas de pila o detalles internos del sistema a los
usuarios finales. Registra los detalles completos en el servidor, devuelve mensajes saneados a los
clientes.

## Regla 6: Seguridad de dependencias
Usa solo dependencias de fuentes aprobadas. Comprueba vulnerabilidades conocidas antes de añadir
paquetes nuevos. Fija las versiones en producción.

## Regla 7: Revisiones de código requeridas
Todos los cambios de código T2+ requieren revisión por pares antes del despliegue. El código
generado por IA está sujeto a los mismos estándares de revisión que el código escrito por humanos.

## Regla 8: Pruebas requeridas
Todo el código de producción debe tener pruebas. Mínimo: pruebas unitarias para la lógica de negocio,
pruebas de integración para los endpoints de API. Objetivo de cobertura: 80% para código nuevo.

## Regla 9: Documentación
Todas las APIs públicas deben tener documentación (OpenAPI/Swagger). La lógica de negocio compleja
debe tener comentarios en línea que expliquen el PORQUÉ, no el QUÉ.

## Regla 10: Conciencia del rendimiento
Considera las implicaciones de rendimiento del código generado. Evita consultas N+1, bucles no
acotados, y asignación excesiva de memoria. Usa paginación para los endpoints de listado.
</content>
