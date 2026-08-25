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

# PROJECT_CONTEXT.md

## Organización
Atexis — departamento de TI que gestiona la plataforma de desarrollo asistido por IA.

## Visión general de la plataforma
La Plataforma de Gestión de IA (IdAI) provee gobernanza centralizada, despliegue y monitorización para
todas las iniciativas de desarrollo asistido por IA dentro de la organización.

## Sistemas clave
- **Consola de Administración**: UI de gestión central (React + FastAPI)
- **Aplicación IdAI**: Frontend de gobernanza de iniciativas (React + shadcn/ui)
- **Plataforma MCP**: Servidores del Protocolo de Contexto de Modelo para integración de herramientas
- **ToolHive**: Pasarela de herramientas con el motor de políticas Cedar
- **Keycloak**: Gestión de identidad y accesos
- **Pila de inferencia**: Inferencia de LLM local (Ollama + vLLM en RTX 4090)

## Sistema de niveles
- **T1 (Autoservicio)**: Asistentes de codificación de IDE, sin datos sensibles
- **T2 (Aprobado por IS)**: Herramientas internas, infraestructura compartida
- **T3 (IS + Datos)**: Acceso al almacén de datos, VMs dedicadas
- **T4 (IS + TI + Producción)**: Sistemas de producción, despliegue conjunto

## Clasificación de datos
- **Público**: Sin restricciones
- **Interno**: Solo interno de la organización
- **Confidencial**: Base de necesidad de conocer, cifrado
- **Estrictamente Confidencial**: Controles máximos, procesamiento aislado

## Infraestructura
- **APP024**: Servidor primario de inferencia de IA (RTX 4090, 60GB RAM, Ubuntu 24.04)
- **Docker**: Todos los servicios en contenedores
- **Redes**: Solo red interna, HTTPS/TLS en todas partes
- **Autenticación**: Keycloak OIDC con acceso basado en roles

## Equipos
- **IS (Seguridad de la Información)**: Política, cumplimiento, revisiones
- **Infraestructura de TI**: Aprovisionamiento, redes, monitorización
- **Desarrollo**: Construcción y despliegue de herramientas de IA
- **Unidades de negocio**: Consumidores de las capacidades de IA
</content>
