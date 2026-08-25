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

# SECURITY_CONTEXT.md

## Modelo de amenazas
La plataforma de IA procesa datos internos de la organización y confidenciales. Amenazas principales:
- Exfiltración de datos vía salidas de modelos de IA
- Ataques de inyección de prompts
- Acceso no autorizado a capacidades elevadas
- Compromiso de la cadena de suministro de herramientas de IA
- Amenaza interna vía acceso offshore

## Controles de seguridad

### Autenticación
- Keycloak OIDC con MFA para todos los usuarios
- Cuentas de servicio para la comunicación entre servicios
- Validación de token JWT en cada solicitud de API
- Gestión de sesión: tiempo de espera de inactividad de 30 minutos, máximo 4 sesiones concurrentes

### Autorización
- Control de acceso basado en roles (RBAC) vía grupos de Keycloak
- Restricción de capacidades por nivel (políticas Cedar en ToolHive)
- Filtrado de acceso basado en la clasificación de datos
- Restricciones de rol onshore/offshore para sistemas T3+

### Seguridad de red
- Todos los servicios en red interna (sin exposición a internet público)
- TLS 1.3 para toda la comunicación entre servicios
- Pasarela NGINX con limitación de tasa (30 solicitudes/min, ráfaga de 10)
- Módulo de autenticación NJS valida tokens a nivel de pasarela
- Prevención de SSRF: bloquea rangos de IP privados en solicitudes salientes

### Protección de datos
- Aplicación de clasificación de datos en todos los endpoints de API
- Escaneo DLP en comunicaciones salientes
- Registro de auditoría de todo acceso a datos
- Ningún dato persiste en localStorage/sessionStorage (solo base de datos)
- Cifrado en reposo para datos confidenciales

### Controles específicos de IA
- El filtrado de respuestas elimina datos clasificados de las salidas de LLM
- El acceso a herramientas está restringido por el nivel del usuario y la sensibilidad de los datos
- Se requiere completar la formación antes de acceder a las herramientas
- Detección de anomalías en los patrones de uso (volumen, temporización, acceso a datos)
- La protección de ramas evita que las herramientas de IA hagan push a ramas protegidas

### Cumplimiento
- Alineación con ISO 27001 (EPO-GISS-005)
- GDPR: derechos del interesado, consentimiento, DPIAs
- EU AI Act: categorización de riesgo, explicabilidad, registro del Artículo 27
- Revisiones de seguridad trimestrales para iniciativas T2+

### Respuesta a incidentes
- Alertas automatizadas ante la detección de anomalías
- Ruta de escalado: guardia de IS → líder de IS → CISO
- Interruptor de emergencia (kill switch): deshabilita cualquier herramienta/usuario vía la consola de administración
- Los logs de auditoría se retienen un mínimo de 90 días
</content>
