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

# Guías de Revisión de Código

## Propósito
Todo el código T2+ (escrito por humanos o generado por IA) debe pasar una revisión por pares antes del
despliegue. El código generado por IA recibe el mismo escrutinio que el código escrito por humanos.

## Proceso de revisión

### Responsabilidades del remitente
1. Autorrevisión antes de solicitar la revisión por pares
2. Asegurarse de que todas las pruebas pasan localmente
3. Incluir una descripción de qué cambió y por qué
4. Señalar explícitamente cualquier sección generada por IA
5. Confirmar el cumplimiento de AACF (reglas, seguridad, clasificación)

### Responsabilidades del revisor
1. Verificar la corrección (¿hace lo que afirma?)
2. Comprobar la seguridad (OWASP Top 10, validación de entrada, autenticación)
3. Verificar el cumplimiento de AACF (sigue las plantillas y reglas)
4. Comprobar el manejo de datos (apropiado según la clasificación)
5. Evaluar el rendimiento (sin consultas N+1, sin bucles no acotados)
6. Validar las pruebas (cobertura adecuada, aserciones significativas)

## Checklist de revisión

### Seguridad
- [ ] Sin credenciales o secretos hardcodeados
- [ ] Validación de entrada en todos los endpoints
- [ ] Comprobaciones de autorización presentes
- [ ] Sin vectores de inyección SQL
- [ ] Sin vectores de XSS
- [ ] Los datos sensibles no se registran en logs
- [ ] Prevención de SSRF para el manejo de URLs

### Calidad de código
- [ ] Sigue las reglas específicas del lenguaje (ficheros .mdc)
- [ ] Manejo de errores presente y apropiado
- [ ] Sin código muerto ni imports sin usar
- [ ] Nomenclatura clara y consistente
- [ ] La lógica compleja tiene comentarios que explican el PORQUÉ

### Manejo de datos
- [ ] Respeta el nivel de clasificación de datos
- [ ] El manejo de datos personales cumple con GDPR
- [ ] Registro de auditoría para el acceso a datos
- [ ] Sin fuga de datos vía mensajes de error

### Comprobaciones específicas de IA
- [ ] El código generado por IA ha sido comprendido (no aceptado a ciegas)
- [ ] Sin APIs alucinadas ni funciones inexistentes
- [ ] Las dependencias realmente existen y son compatibles
- [ ] La lógica de negocio es correcta (no solo sintácticamente válida)

## Requisitos de aprobación

| Nivel | Aprobadores requeridos | Tiempo máximo de revisión |
|------|--------------------|-----------------|
| T1 | 0 (autoservicio) | N/A |
| T2 | 1 revisor par | 2 días hábiles |
| T3 | 1 par + 1 IS | 3 días hábiles |
| T4 | 2 pares + 1 IS + 1 IT | 5 días hábiles |

## Escalado
Si un revisor identifica un problema de seguridad crítico:
1. Bloquear la revisión inmediatamente
2. Notificar al equipo de IS
3. No fusionar hasta que IS dé el visto bueno
</content>
