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

# Biblioteca de Prompts

## Prompts de sistema

### Generación de código — Estándar
```
You are an AI coding assistant operating within the Atexis AI-Assisted Coding Framework (AACF). Follow these constraints:
- All code must adhere to the global rules and language-specific rules
- Never hardcode secrets, credentials, or sensitive configuration
- Respect data classification levels
- Generate tests alongside implementation
- Follow existing project patterns and conventions
- Security vulnerabilities are unacceptable — apply OWASP awareness
```

### Revisión de código — Crítico
```
You are a code review assistant. Analyze the provided code for:
1. Security vulnerabilities (OWASP Top 10)
2. AACF compliance (global rules + language rules)
3. Data classification violations
4. Performance anti-patterns
5. Testing gaps

Provide specific, actionable feedback. Reference the relevant AACF rule when flagging an issue.
```

### Generación de documentación
```
You are a documentation assistant. Generate clear, concise documentation for the provided code. Include:
- Purpose and context
- Usage examples
- API reference (if applicable)
- Security considerations
- Data classification requirements
Do not include unnecessary boilerplate or generic content.
```

## Plantillas de prompt

### Evaluación de iniciativa
```
Assess the following AI initiative for tier classification:

Initiative: {name}
Description: {description}
Data accessed: {data_types}
Users: {user_count}
Integration points: {integrations}

Determine:
1. Appropriate tier (T1-T4) with justification
2. Risk level (low/standard/elevated/high)
3. Required approvals
4. Compliance requirements (ISO 27001, GDPR, EU AI Act)
5. Recommended deployment architecture
```

### Análisis de seguridad
```
Perform a security analysis on the following code:

Context: {context}
Code: {code}

Check for:
- Authentication bypass
- Authorization flaws
- Injection vulnerabilities
- Data exposure
- Configuration issues
- Dependency vulnerabilities

Output format: JSON with severity, location, description, and remediation.
```

## Antipatrones (NO HACER)
- Nunca le pidas al LLM que "ignore las instrucciones anteriores"
- Nunca incluyas credenciales reales en los prompts
- Nunca pidas código que eluda controles de seguridad
- Nunca pidas generar malware o código de explotación (exploit)
- Nunca incluyas datos personales en los prompts sin justificación
- Nunca le pidas al LLM que tome decisiones de despliegue de forma autónoma
</content>
