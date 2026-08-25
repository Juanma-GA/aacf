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

# Changelog de AACF

## [2.0.0] - 2026-07-15

Expansión mayor hacia un framework completo: barreras de seguridad + UX unificada + seguridad/gobernanza/cumplimiento +
un catálogo de agentes, construido sobre una revisión profunda del estado del arte de mediados de 2026 (`docs/STATE_OF_THE_ART.md`).

### Añadido
- **`AGENTS.md`** — punto de entrada agnóstico de herramienta que sigue el estándar abierto AGENTS.md (la
  capa base de instrucciones convergente para Claude Code / Cursor / Copilot / Windsurf / Gemini CLI / Codex).
- **`rules/atexis-hard-rules.md`** — las Reglas Estrictas de ATEXIS HR0–HR21 incorporadas al framework.
- **`rules/ai-output-safety.mdc`** — reglas de seguridad específicas para código generado por IA (OWASP LLM Top 10,
  slopsquatting/cadena de suministro, inyección de prompts, agencia excesiva, salida estructurada como control).
- **`agents/`** — catálogo de agentes: los cuatro agentes de espacio de trabajo (checklist-creator / -implementer /
  -validator, codebase-hardening) migrados, más los nuevos agentes **senior-prompt-engineer**, **code-reviewer**,
  y **security-reviewer**, con un README del catálogo y la composición del camino de referencia.
- **`governance/security-governance-compliance.md`** — catálogo de controles que mapea ISO/IEC 27001
  (A.8.25–A.8.34), GDPR, NIST SSDF/800-218A, SLSA, OWASP web + LLM Top 10, y la EU AI Act, con un
  mapeo cruzado de una-implementación-muchas-auditorías.
- **`governance/guardrails.md`** — aplicación determinista y no eludible (hooks de pre-commit,
  puertas de escaneo de secretos, lista blanca de dependencias + periodo de espera, protección de ramas, puertas HITL, registro de auditoría,
  DLP) con un umbral mínimo por nivel.
- **`styles/design-system.md`** — el framework UX unificado en tres capas (tokens DTCG → preset shadcn
  `registry:base` → Skill/MCP de agente), con criterios de aceptación WCAG 2.2 AA.
- **`docs/STATE_OF_THE_ART.md`** — la base de investigación de mediados de 2026 con fuentes.

### Cambiado
- `styles/branding.md` — corregida la paleta primaria a **azul ATEXIS `#2E74B5`**; se anotó la fuente de
  verdad de tokens DTCG/OKLCH.
- `README.md` — reescrito en torno a los cuatro pilares del framework y un índice.
- Los agentes migrados ahora hacen referencia a `rules/atexis-hard-rules.md` en lugar del `CLAUDE.md` del espacio de trabajo.

## [1.0.0] - 2025-01-01

### Añadido
- Estructura inicial del framework AACF
- Plantillas: web-app, api-service, data-pipeline, internal-tool, automation-script
- Reglas globales (10 reglas)
- Reglas de IDE: atexis-global.mdc, security.mdc, python.mdc, javascript.mdc, dotnet.mdc
- Ficheros de contexto de proyecto: PROJECT_CONTEXT.md, SECURITY_CONTEXT.md
- Definiciones de la biblioteca de componentes del kit de UI
- Activos de marca (colores, fuentes, tokens de diseño)
- Checklists de gobernanza por nivel (T1-T4)
- Guías de revisión de código
- Biblioteca de prompts (prompts de sistema, plantillas, antipatrones)
</content>
