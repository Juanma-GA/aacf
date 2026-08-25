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

# Barreras de Seguridad (Guardrails) — Aplicación Determinista

Las reglas que un agente puede leer son de carácter consultivo; las reglas que **aplica un pipeline**
son garantías. Los agentes pueden racionalizar en torno a la prosa pero no pueden pasar por alto un
hook que falla. Este documento enumera las barreras *mecánicas* que el AACF espera que todo proyecto de
ATEXIS conecte — la capa de aplicación bajo el catálogo de controles en
[`security-governance-compliance.md`](security-governance-compliance.md).

La "pila de políticas" del profesional de 2026 para agentes de codificación tiene seis capas:
**escaleras de permisos → hooks previos a la herramienta → sandboxes del SO → interrupciones de humano
en el bucle → tokens vinculados a la audiencia → mapa de amenazas (OWASP)**. Reserva los ficheros de
reglas para el juicio; usa esta capa para los no negociables.

## Las capas de aplicación

| # | Barrera | Mecanismo | Aplica |
|---|-----------|-----------|----------|
| G1 | **Hooks de pre-commit** | Escaneo de secretos + lint + bloqueo de patrones prohibidos que puede *rechazar* el commit. Se ejecuta ya sea que un humano, un agente, o un script haga el commit. | HR8; ISO A.8.28; SSDF PS.1 |
| G2 | **Escaneo de secretos como puerta de fusión dura** | Tres niveles — en el momento del commit (detect-secrets), en el momento del PR (Gitleaks), análisis de contenido en CI. | LLM02; ISO A.8.24 |
| G3 | **Lista blanca de dependencias + periodo de espera de instalación + verificación de hash/lockfile** | CI bloquea paquetes fuera de la lista blanca y paquetes con menos de 30–90 días de antigüedad; `npm ci` / `pip` con hashes; SBOM en cada build. | LLM03 (slopsquatting); ISO A.5.21; SLSA |
| G4 | **Detección de paquetes alucinados** | Un paso de CI verifica que cada dependencia existe + su fecha de registro *antes* de instalarla (p. ej. sloppy-joe / supply-chain-guard). | LLM03; SSDF PW.4 |
| G5 | **Protección de ramas — ningún push de agente a `main`** | Los agentes trabajan solo en ramas de funcionalidad; se requiere PR + revisión para fusionar; `main` permanece como un punto de retorno limpio. | LLM06; ISO A.8.31/8.32 |
| G6 | **Puertas de revisión / interrupciones de humano en el bucle** | Aprobación humana obligatoria antes de acciones destructivas/de alto privilegio del agente y antes de fusionar. | LLM06; ISO A.8.32; supervisión humana de la AI Act |
| G7 | **Registro de auditoría de cada acción de IA** | Acceso a ficheros, comando de shell, PR, llamada a API → transmitido al SIEM, consultable. | ISO A.8.15; registro de la AI Act; ISO 42001 |
| G8 | **DLP en los prompts** | Inspeccionar/redactar/bloquear datos personales, secretos, y código fuente confidencial que sale hacia modelos externos. | GDPR Art. 5/28; ISO A.8.24 |
| G9 | **Sandboxing del SO + tokens de mínimo privilegio, de corta duración, vinculados a la audiencia** para los agentes. | Limita el radio de impacto de un agente desviado/comprometido. | LLM06; ISO A.8.27 |
| G10 | **Puertas de seguridad de CI** — SAST + SCA + DAST deben pasar para fusionar/lanzar. | Detecta defectos de inyección/autorización/dependencias de forma determinista. | SSDF PW.7/8; ISO A.8.29 |
| G11 | **Fundamentación (grounding) + temperatura baja** para asistentes de codificación | Fundamentar con RAG; reducir la aleatoriedad para disminuir los paquetes/APIs alucinados (el 43% de las alucinaciones son repetibles). | LLM03/LLM09 |
| G12 | **Límites de tasa y cuotas de coste** por usuario y por repositorio, con un watchdog. | Acota los bucles de agente desbocados sin un límite duro silencioso. | LLM10; HR18/HR19 |

## Cómo se corresponde esto con las Reglas Estrictas de AACF

- **HR7 (sin fallback degradante)** y **HR18/HR19 (watchdog + escalado, sin límites duros)** significan
  que una barrera *bloquea y escala* — nunca degrada o trunca en silencio para seguir adelante.
- **HR6 (sin truncado; JSON guiado)** es en sí misma una barrera: la salida estructurada cierra clases
  de exfiltración por construcción.
- **HR9 (sin operaciones destructivas de datos sin confirmación)** se aplica mediante G6 (interrupción
  humana) + G5 (protección de ramas).

## Umbral mínimo por nivel

| Nivel | Barreras requeridas |
|------|---------------------|
| **T1** (autoservicio, sin datos sensibles) | G1, G11, G12; las salidas se revisan antes del commit. |
| **T2** (herramientas internas) | + G2, G3, G5, G6, G7, G10. |
| **T3** (acceso al almacén de datos) | + G4, G8, G9; control de acceso a nivel de columna; DLP en todas las salidas. |
| **T4** (producción / expuesto al cliente) | Todas las G1–G12; procedencia (SLSA L3) en los artefactos de lanzamiento; monitorización 24/7 + detección de anomalías. |

Los niveles se definen en [`tier-checklists.md`](tier-checklists.md) (el modelo de madurez de
gobernanza del AACF; la plataforma IdAI usa un esquema T1–T5 + T1-E más fino basado en exposición —
mapea al nivel de AACF más cercano). El
[`security-reviewer`](../agents/security-reviewer.agent.md) verifica que las barreras requeridas están
presentes para el nivel objetivo; [`codebase-hardening`](../agents/codebase-hardening.agent.md) es el
pase completo de endurecimiento de producción para T3+.

> **El movimiento individual más sólido:** hacer que las barreras sean deterministas y no eludibles —
> hooks de pre-commit (G1), puertas duras de fusión con escaneo de secretos (G2), protección de ramas
> que impide el push de agentes a `main` (G5), y registro de auditoría completo hacia el SIEM (G7).
> Todo lo demás es defensa en profundidad encima de eso.
</content>
