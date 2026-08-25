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

# AGENTS.md — ATEXIS AI-Assisted Coding Framework (AACF)

Este es el **punto de entrada agnóstico de herramienta** para cualquier agente de codificación con IA
(Claude Code, Cursor, Copilot, Windsurf, Gemini CLI, Codex, …) que trabaje en un proyecto "vibe coding"
de ATEXIS. Sigue el estándar abierto [`AGENTS.md`](https://agents.md) para que un único fichero
gobierne todas las herramientas. Haz que tu fichero específico de cada harness apunte aquí:
`CLAUDE.md`, `.cursor/rules/` y `.github/copilot-instructions.md` deberían referenciar este en
lugar de derivar en N copias.

> **Una regla por encima de todas:** el AACF es la fuente única de verdad. Construye sobre
> componentes, reglas y patrones aprobados — nunca desde cero, nunca en contra de las barreras de
> seguridad (guardrails).

## Frescura de versión — verifica antes de confiar (una vez por sesión)

Si estás leyendo ficheros de AACF desde una **copia local/descargada** (una instantánea ZIP, ficheros
comiteados en un repositorio), verifica que la copia está actualizada antes de confiar en ella:

1. Llama a la herramienta MCP **`aacf_fetch`** (endpoint `http://10.117.139.1:8200/mcp`, streamable-http,
   `Authorization: Bearer <key>` o `X-Api-Key`): `aacf_fetch({"path": "VERSION"})`.
2. Compárala con el `aacf/VERSION` local. Si difieren, tu copia está **desactualizada**: vuelve a
   descargar TODOS los ficheros del framework vía `aacf_fetch` (recorre desde `{"path": "", "list_dir": true}`)
   o pide al usuario que vuelva a descargar el ZIP del framework desde el portal IdAI
   (`/propose/framework`). Nunca mezcles ficheros de distintas versiones del framework.
3. Si **no puedes acceder** al MCP `aacf_fetch`, debes decírselo al usuario: *no puedes acceder al
   MCP de AACF y podrías estar trabajando con una versión desactualizada del framework* — luego
   continúa con la copia local.

El contenido obtenido en vivo vía `aacf_fetch` es siempre la versión desplegada — no requiere comprobación.

## Qué te ofrece el AACF

- **Barreras de seguridad (guardrails)** — las Reglas Estrictas de ATEXIS y las reglas de seguridad de
  salida de IA que el código generado por IA debe cumplir.
- **Un framework de diseño UX unificado** — un único sistema de diseño (tokens → componentes → reglas
  de agente) para que cada proyecto luzca y se comporte de forma consistente, no generado al azar.
- **Controles de seguridad + gobernanza + cumplimiento** basados en ISO/IEC 27001, GDPR y un ciclo de
  vida de desarrollo de software seguro (Secure SDLC) (OWASP web + LLM Top 10, NIST SSDF, EU AI Act).
- **Un catálogo de agentes especializados** — planificador, implementador, validador, revisores, un
  ingeniero de prompts senior y un agente de endurecimiento para producción.

## Lee esto antes de escribir código

| Capa | Fichero | Propósito |
|-------|------|---------|
| **Reglas Estrictas** | [`rules/atexis-hard-rules.md`](rules/atexis-hard-rules.md) | HR0–HR21 — no negociables. Autocomprueba cada cambio contra ellas. |
| Reglas globales | [`rules/global_rules.md`](rules/global_rules.md) | Estándares de ingeniería base. |
| Reglas de seguridad | [`rules/security.mdc`](rules/security.mdc) | Autenticación, validación de entrada, inyección, secretos. |
| Seguridad de salida de IA | [`rules/ai-output-safety.mdc`](rules/ai-output-safety.mdc) | Barreras específicas para código generado por IA (OWASP LLM Top 10). |
| Reglas por lenguaje | `rules/python.mdc` · `rules/javascript.mdc` · `rules/dotnet.mdc` | Estándares por lenguaje (con ámbito por glob). |
| Sistema de diseño | [`styles/design-system.md`](styles/design-system.md) | El framework UX unificado: tokens, componentes, accesibilidad (a11y). |
| Marca / kit de UI | [`styles/branding.md`](styles/branding.md) · [`styles/ui-kit.md`](styles/ui-kit.md) | Colores, tipografía, convenciones de componentes. |
| Gobernanza | [`governance/`](governance/) | Guías de revisión, barreras deterministas, el catálogo completo de controles de cumplimiento, checklists por nivel. |
| Agentes | [`agents/`](agents/README.md) | Definiciones de agentes especializados y cómo se combinan. |
| Prompts | [`prompts/prompt-library.md`](prompts/prompt-library.md) | Prompts de sistema y plantillas aprobados. |
| Contexto | [`docs/PROJECT_CONTEXT.md`](docs/PROJECT_CONTEXT.md) · [`docs/SECURITY_CONTEXT.md`](docs/SECURITY_CONTEXT.md) | Contexto de plataforma y amenazas. |
| Estado del arte | [`docs/STATE_OF_THE_ART.md`](docs/STATE_OF_THE_ART.md) | La investigación en la que se basa este framework (mediados de 2026). |

## Cómo trabajar (el camino de referencia)

1. **Especificación primero.** Acuerda *qué* construir antes de *cómo* — una especificación/checklist
   breve que nombre los ficheros e interfaces afectados, indique qué queda fuera del alcance, y termine
   con un paso de verificación ejecutable. Usa `checklist-creator` → `checklist-validator`.
2. **Parte de componentes aprobados.** Instala el sistema de diseño y reutiliza las plantillas de AACF
   (`templates/`) — no fabriques primitivas a mano.
3. **Implementa por completo.** Sin stubs, sin `TODO`, sin datos simulados. Conecta backend ↔ frontend
   ↔ BD ↔ API. Usa `checklist-implementer`.
4. **Verifica contra las Reglas Estrictas** y ejecuta una comprobación ejecutable (tests / build / captura
   de pantalla).
5. **Revisa antes de dar por "hecho".** `code-reviewer` en cada diff; `security-reviewer` para cambios
   críticos de seguridad; `senior-prompt-engineer` para cualquier cosa orientada a LLM. Las revisiones
   se ejecutan en contextos aislados.
6. **Endurece antes de producción.** `codebase-hardening` para código T3+ / de cara al cliente / entregable.

## No negociables (la lista corta — texto completo en las Reglas Estrictas)

- **Nada hardcodeado** — todo configurable, la configuración se refleja en una superficie de administración (HR0/HR8).
- **Sin almacenamiento del navegador para el estado** — Postgres es la fuente de verdad; autenticación vía cookies HTTP-only (HR1).
- **Fusión profunda (deep-merge) de configuración anidada**, nunca superficial (HR10).
- **Sin truncar contenido** — resume con un LLM, prefiere JSON guiado antes que `max_tokens` (HR6).
- **Sin fallbacks degradantes** — escala, no reduzcas la calidad en silencio (HR7).
- **UI de mutación optimista** — refleja de inmediato, revierte si el servidor rechaza (HR20).
- **Humaniza todo el texto de la UI** — nunca muestres tokens de máquina en crudo (HR21).
- **Localiza todo el texto de la UI** (HR15).
- **Nada de `from __future__ import annotations` en ficheros de rutas FastAPI** — rompe `include_router()`.
- **Sin timeouts fijos / bucles con `sleep` / `max_iterations`** en código agéntico — watchdog + escalado
  (HR18/HR19).
- **Trata toda salida de IA como no confiable** — valida antes de ejecutar/renderizar; verifica que cada
  dependencia sugerida por IA existe y está fijada (cadena de suministro / slopsquatting).

## Convenciones de redacción de reglas (cuando añadas reglas)

Cortas, **una preocupación por regla**, imperativas ("debe", no "preferiblemente"), **con ámbito por
glob** para que una regla se cargue solo para los ficheros que gobierna, `alwaysApply` reservado para
restricciones genuinamente universales. Lleva el conocimiento profundo y ocasionalmente relevante a
una Skill o una regla con ámbito, no a la base siempre cargada — un fichero de instrucciones sobrecargado
hace que los agentes ignoren las reglas que sí importan.

---

*Versión de AACF: ver [`VERSION`](VERSION). Este fichero es la base que hereda todo proyecto ATEXIS;
mantenlo como la única fuente de verdad y añade encima los ficheros específicos de cada herramienta.*
</content>
