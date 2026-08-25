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

# ATEXIS AI-Assisted Coding Framework (AACF)

El AACF es la fuente única de verdad para el desarrollo asistido por IA ("vibe coding") en ATEXIS. Provee
las **barreras de seguridad (guardrails)**, un **framework de diseño UX unificado**, **controles de seguridad
+ gobernanza + cumplimiento** (ISO/IEC 27001, GDPR, Secure SDLC), un catálogo de **agentes especializados**, y
plantillas, reglas, estilos y prompts aprobados — para que cada proyecto se construya sobre componentes y
estándares aprobados, no desde cero.

**Empieza aquí:** [`AGENTS.md`](AGENTS.md) — el punto de entrada agnóstico de herramienta que lee todo agente de codificación con IA.

## Estructura de directorios

```
aacf/
├── AGENTS.md            # ← punto de entrada agnóstico de herramienta (estándar abierto AGENTS.md)
├── rules/               # Reglas Estrictas (HR0–HR21), reglas globales/de seguridad/de seguridad-IA + reglas de lenguaje (.mdc)
├── agents/              # Definiciones de agentes especializados (planificador, revisores, ingeniero de prompts, endurecimiento)
├── governance/          # Catálogo de controles de cumplimiento, barreras deterministas, guías de revisión, niveles
├── styles/              # Sistema de diseño unificado: tokens, marca, kit de UI
├── prompts/             # Prompts de sistema y plantillas aprobados
├── templates/           # Plantillas de andamiaje de proyecto
└── docs/                # Contexto de proyecto y de seguridad, investigación del estado del arte
```

## Qué hay dentro

| Necesidad | Ir a |
|------|-------|
| Las reglas de ingeniería no negociables | [`rules/atexis-hard-rules.md`](rules/atexis-hard-rules.md) (HR0–HR21) |
| Seguridad para código generado por IA | [`rules/ai-output-safety.mdc`](rules/ai-output-safety.mdc) |
| Una UX unificada y consistente | [`styles/design-system.md`](styles/design-system.md) |
| Controles de seguridad / gobernanza / cumplimiento | [`governance/security-governance-compliance.md`](governance/security-governance-compliance.md) |
| Aplicación determinista (hooks, puertas) | [`governance/guardrails.md`](governance/guardrails.md) |
| Agentes especializados y cómo se combinan | [`agents/README.md`](agents/README.md) |
| La investigación en la que se basa esto | [`docs/STATE_OF_THE_ART.md`](docs/STATE_OF_THE_ART.md) |

## Uso

El contenido se sirve en modo de solo lectura vía la herramienta MCP `aacf_fetch`; todos los niveles tienen
acceso. IdAI importa los documentos de gobernanza/reglas como documentos de referencia
(`POST /idai/reference/import-aacf`). El repositorio del framework es el punto de partida del camino de
referencia — descárgalo para construir sobre componentes aprobados. Los mantenedores del framework
actualizan el contenido a través de la superficie de administración de AACF en la consola de administración.

## Versionado

Todos los cambios se rastrean vía git; las versiones de contenido siguen semver. Ver [`CHANGELOG.md`](CHANGELOG.md).
**Versión actual: 2.0.0**
</content>
