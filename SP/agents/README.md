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

# Agentes de AACF

Definiciones de agentes especializados para el desarrollo asistido por IA en ATEXIS. Cada agente es un
fichero Markdown con front-matter YAML (`name`, `description` escrito como **condiciones de disparo**,
lista opcional de `tools` permitidas y `model`) seguido del prompt de sistema del agente. Este formato
es el estándar convergente de 2025–26 (Claude Code `.claude/agents/`, y portable a Cursor / Copilot /
otros harnesses).

Dos principios de diseño tomados del estado del arte (`../docs/STATE_OF_THE_ART.md`):

- **Contexto aislado por agente** — cada uno se ejecuta en su propia ventana de contexto, manteniendo
  limpia la sesión principal y permitiendo que un revisor evalúe un cambio sin ver el razonamiento que
  lo produjo.
- **Herramientas de mínimo privilegio** — los revisores no tienen **acceso de escritura**; un agente
  de documentación no tiene shell. Restringe la lista de `tools` permitidas a lo que el rol necesita.

## Catálogo

| Agente | Rol | Cuándo usarlo |
|-------|------|-------------|
| [checklist-creator](checklist-creator.agent.md) | Convierte una solicitud en un checklist de implementación accionable y fundamentado en el código base. | Al inicio de cualquier funcionalidad no trivial. |
| [checklist-implementer](checklist-implementer.agent.md) | Ejecuta un checklist elemento por elemento — código completo, cero marcadores de posición, verificado en despliegue. | Al construir a partir de un checklist acordado. |
| [checklist-validator](checklist-validator.agent.md) | Valida un checklist/arquitectura contra el código base y las buenas prácticas; encuentra huecos y riesgos. | Antes de comprometerse con un plan. |
| [code-reviewer](code-reviewer.agent.md) | Revisión adversarial del diff contra las reglas de AACF + las Reglas Estrictas, en un contexto nuevo. | Antes de marcar como hecho cualquier cambio T2+; en cada diff generado por IA. |
| [security-reviewer](security-reviewer.agent.md) | Auditoría de seguridad y cumplimiento (OWASP web + LLM Top 10, ISO 27001, GDPR, SSDLC). | Cambios de autenticación/datos/dependencias/LLM/infraestructura; antes de un despliegue T3+. |
| [senior-prompt-engineer](senior-prompt-engineer.agent.md) | Diseña, prueba, versiona y endurece prompts / instrucciones de agente / esquemas de salida estructurada. | Cualquier funcionalidad orientada a LLM; prompts poco fiables, con alucinaciones o inyectables. |
| [codebase-hardening](codebase-hardening.agent.md) | Lleva código "vibe-coded" / generado por IA a producción — clasifica el modo de despliegue, obtiene la política corporativa, dirige el endurecimiento de seguridad + cadena de suministro + gobernanza con un informe respaldado por evidencia. | Promover un POC a un despliegue real (T3+ / de cara al cliente / entregable). |

## El camino de referencia (cómo se combinan)

```
checklist-creator ──▶ checklist-validator ──▶ checklist-implementer
                                                     │
                            ┌────────────────────────┼────────────────────────┐
                            ▼                        ▼                         ▼
                     code-reviewer          security-reviewer        senior-prompt-engineer
                       (cada diff)      (crítico de seguridad)        (orientado a LLM)
                                                     │
                                                     ▼
                                            codebase-hardening
                                          (antes de producción / T3+)
```

Las revisiones se ejecutan en **contextos aislados** y son **adversariales** — un revisor nuevo al que
se le indica señalar solo los huecos que afectan a la corrección, la seguridad o el requisito
establecido (no inventar trabajo). Flujo dirigido por especificación: acuerda primero el
checklist/especificación, implementa contra él, verifica con una comprobación ejecutable, y luego
revisa antes de dar por "hecho."

Consulta `../rules/atexis-hard-rules.md` para las Reglas Estrictas que aplica cada agente, y
`../governance/` para las guías de revisión, las barreras y los controles de cumplimiento en los que se apoyan.
</content>
