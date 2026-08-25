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

# Estado del arte — Frameworks de codificación asistida por IA (mediados de 2026)

La base de investigación sobre la que se construye el AACF v2.0: cómo estructuran los equipos punteros
las capas de reglas/agente/UX/gobernanza para el desarrollo asistido por IA, a mediados de 2026. Este
es el "enfoque más reciente y mejor informado" — se mantiene en el framework para que el *por qué*
detrás de cada convención sea auditable.

---

## 1. La capa de reglas y agentes — la convergencia

El caos de ficheros por herramienta de 2024 ha convergido hacia estándares abiertos:

- **`AGENTS.md` ganó la capa de instrucciones base.** Markdown plano, sin campos obligatorios, gana el
  fichero más cercano en monorepos. Formalizado como una especificación abierta (agosto de 2025),
  donado a la Agentic AI Foundation de la Linux Foundation (diciembre de 2025), adoptado por más de
  60.000 repositorios y leído nativamente por Codex, Cursor, Copilot, Gemini CLI, Aider, Windsurf,
  Zed. **Este es el fichero de mayor apalancamiento a añadir.** ([agents.md](https://agents.md))
- **Reglas con ámbito acotado, no un volcado.** Cursor `.cursor/rules/*.mdc` (front-matter
  `description`/`globs`/`alwaysApply`; cuatro modos de activación), Copilot `*.instructions.md` (glob
  `applyTo`), Cline `.clinerules/` (front-matter de rutas). Buena práctica: **5–8 reglas, una
  preocupación cada una, voz imperativa, con ámbito por glob, `alwaysApply` solo para restricciones
  universales.** El formato de fichero único `.cursorrules` está obsoleto.
- **`CLAUDE.md` + `.claude/agents/*.md` + Skills** para Claude Code. La propia guía de Anthropic:
  *"Los ficheros CLAUDE.md sobrecargados hacen que Claude ignore tus instrucciones reales"* — la
  prueba de fuego por línea es "¿eliminar esto causaría un error? Si no, córtalo." El conocimiento a
  veces relevante → **Skills (divulgación progresiva)**, no la base siempre cargada.
  ([Buenas prácticas de Claude Code](https://code.claude.com/docs/en/best-practices))

**Patrones de subagentes:** agentes especializados en sus propios ficheros con una `description` de
**condición de disparo** y una **lista mínima de herramientas permitidas** (los revisores no tienen
escritura). Orquestador-trabajador (el líder planifica → escribe el plan en memoria → distribuye
trabajadores con tareas autocontenidas + formatos de salida explícitos) superó al agente único en más
del 90% en el sistema de investigación de Anthropic. **Separación autor/revisor** — un revisor nuevo ve
solo el diff + los criterios — pero indícale que *"señale solo los huecos que afectan a la corrección o
los requisitos,"* o inventará trabajo.
([Multiagente de Anthropic](https://www.anthropic.com/engineering/), [subagentes](https://code.claude.com/docs/en/sub-agents))

**Desarrollo dirigido por especificación:** GitHub **Spec Kit** (Specify → Plan → Tasks → Implement,
~111k★, agnóstico de agente) y la variante entrevista→`SPEC.md`→sesión-nueva de Anthropic. Las
especificaciones nombran los ficheros/interfaces afectados, indican qué queda fuera de alcance, y
terminan con un **paso de verificación ejecutable**. ([github/spec-kit](https://github.com/github/spec-kit))

**La ingeniería de contexto** reemplazó a la ingeniería de prompts como habilidad principal. El
"deterioro del contexto" (context rot) está medido — todos los modelos de vanguardia se degradan a
medida que crece la entrada. Soluciones: poda despiadada, divulgación progresiva, recuperación justo a
tiempo, memoria externa, conjuntos pequeños de herramientas, y **hooks deterministas para las reglas
que deben cumplirse siempre** (la prosa a modo de consejo se pierde).
([Sourcegraph](https://sourcegraph.com/blog/context-engineering))

> **Adopción en AACF:** `AGENTS.md` como base agnóstica de herramienta (`../AGENTS.md`); reglas `.mdc`
> con ámbito acotado (una preocupación, con ámbito por glob); un catálogo de agentes con descripciones
> de condición de disparo + herramientas de mínimo privilegio (`../agents/`); camino de referencia
> dirigido por especificación primero; barreras deterministas (`../governance/guardrails.md`) para los
> no negociables.

---

## 2. Seguridad, gobernanza y cumplimiento

- **OWASP Top 10 para Aplicaciones LLM (2025)** es el mapa de amenazas canónico; los riesgos que
  realmente afectan al *desarrollo asistido por IA* son LLM01 inyección, LLM02 divulgación de
  secretos/PII, LLM03 cadena de suministro, LLM05 manejo inadecuado de la salida, LLM06 agencia
  excesiva, LLM09 dependencia excesiva. ([genai.owasp.org/llm-top-10](https://genai.owasp.org/llm-top-10/))
- **El slopsquatting es el riesgo nuevo de mayor señal**: ~19,7% del código generado por IA alucina
  nombres de paquetes y ~43% se repiten, haciéndolos pre-registrables por atacantes. Mitigar con
  listas blancas de dependencias, periodos de espera de instalación, comprobaciones de
  existencia/registro, fijación de lockfile+hash, SBOM. ([Nota de investigación de la CSA](https://labs.cloudsecurityalliance.org/research/csa-research-note-slopsquatting-ai-supply-chain-20260419-csa/))
- **NIST SSDF (SP 800-218)** + el complemento de GenAI **SP 800-218A**; procedencia de build **SLSA**
  (L2 mínimo, L3 alto valor); **Microsoft SDL para IA**. ([NIST SSDF](https://csrc.nist.gov/projects/ssdf))
- **ISO/IEC 27001:2022** controles de desarrollo seguro **A.8.25–A.8.34** + registro (A.8.15),
  criptografía (A.8.24), separación de dev/test/prod (A.8.31); **ISO/IEC 42001:2023** como el paraguas
  de gestión de IA cuyo control de evaluación de impacto unifica la DPIA de GDPR + la gestión de
  riesgos de la AI Act. ([ISO 42001](https://www.iso.org/standard/42001))
- **GDPR**: sin datos personales a modelos externos, minimización de datos, disparadores de DPIA,
  limitación de propósito, RoPA, respeto de los derechos del interesado, términos de no-entrenamiento
  en el DPA.
- **EU AI Act**: clasificar cada sistema; la alfabetización en IA (Art. 4) y las prácticas prohibidas
  (Art. 5) aplican ya; el régimen de alto riesgo se aplaza al **2 de diciembre de 2027** (Digital
  Omnibus) — construye la hoja de ruta ahora; transparencia (Art. 50) = etiquetar las salidas de IA.
- **La medida más sólida son las barreras deterministas y no eludibles**: hooks de pre-commit, puertas
  duras de fusión con escaneo de secretos, listas blancas de dependencias + periodo de espera,
  protección de ramas que impide el push de agentes a `main`, puertas de humano en el bucle, registro
  de auditoría completo hacia el SIEM, DLP en los prompts.

> **Adopción en AACF:** el catálogo de controles completo
> (`../governance/security-governance-compliance.md`), la capa de aplicación
> (`../governance/guardrails.md`), y la regla `ai-output-safety.mdc`.

---

## 3. Framework de diseño / UX unificado

La consistencia entre muchas aplicaciones generadas por IA proviene de **codificar el sistema de diseño
como datos legibles por máquina + reglas legibles por agente** — tres capas:

- **Tokens** — el formato W3C Design Tokens (DTCG) alcanzó su primera especificación estable en
  **2025.10**; se redacta un único `*.tokens.json` (colores OKLCH), compilado con **Style Dictionary**
  en propiedades personalizadas CSS + tema de Tailwind. ([W3C DTCG](https://www.designtokens.org/tr/2025.10/format/))
- **Componentes** — **shadcn/ui** (código en el repositorio, para que el agente pueda leerlo/extenderlo)
  entregado como un preset privado **`registry:base`** (CLI v4, marzo de 2026): `npx shadcn init
  <registry>` es el punto de partida del camino de referencia. ([registro de shadcn](https://ui.shadcn.com/docs))
- **Agente** — una **Skill** de shadcn lee `components.json`/`shadcn info` y aplica las reglas de
  composición; un registro privado sobre **MCP** le da al agente una paleta restringida. Aceptación no
  negociable: tokens semánticos (sin valores en crudo), **WCAG 2.2 AA**, `prefers-reduced-motion`,
  responsive, texto humanizado + localizado. ([Skills de shadcn](https://ui.shadcn.com/docs/skills), [Vercel v0](https://vercel.com/blog/ai-powered-prototyping-with-design-systems))

> **Adopción en AACF:** `../styles/design-system.md` (tres capas), `../styles/branding.md` (tokens),
> `../styles/ui-kit.md` (componentes).

---

## 4. Ingeniería de prompts senior

Lo que distingue a un ingeniero de prompts *senior*: **evaluaciones, versionado, pruebas
adversariales, especificidad de modelo, y seguridad a nivel de sistema** — no cadenas ingeniosas de un
solo uso.

- **Ingeniería de contexto** — presupuesto de atención finito; el conjunto de tokens de alta señal más
  pequeño; recuperación justo a tiempo; compactación + subagentes para el horizonte largo.
  ([Anthropic](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents))
- **Condicional al modelo** — las técnicas no se transfieren: no fuerces CoT en un modelo con
  enrutador de razonamiento; pon la pregunta al final para Gemini; prellena para JSON de Claude.
  ([Guía de prompting de GPT-5 de OpenAI](https://developers.openai.com/cookbook/examples/gpt-5/gpt-5_prompting_guide))
- **La salida estructurada/guiada** es el control de mayor apalancamiento por hora — cierra clases de
  exfiltración por construcción (alineación con HR6).
- **La defensa contra la inyección es a nivel de sistema** — las defensas publicadas dentro del prompt
  se eludieron más del 90% de las veces; aplícala vía esquema + delimitación de herramientas + higiene
  de recuperación + autenticación.
- **Las evaluaciones como puerta de lanzamiento** — corpus de referencia + adversarial, versionado,
  ejecutado en cada cambio de prompt/modelo.

> **Adopción en AACF:** el agente
> [`senior-prompt-engineer`](../agents/senior-prompt-engineer.agent.md) y la sección de ingeniero de
> prompts senior de `../prompts/prompt-library.md`.

---

## La única apuesta duradera

Construir sobre los **estándares abiertos y multiherramienta** — `AGENTS.md` + Skills + Spec Kit
(gobernados por la Linux Foundation / MIT) — en lugar del fichero propietario de un único proveedor.
Eso es lo que mantiene al AACF portable mientras el panorama de herramientas sigue cambiando.

---

*Compilado a mediados de 2026 a partir de fuentes primarias (OWASP, NIST, ISO, W3C, documentación de
Anthropic/OpenAI/Google) y reportes secundarios actuales. Algunas cifras (tasas de alucinación, la
fecha de aplazamiento del Digital Omnibus de la EU AI Act) provienen de reportes secundarios —
verifícalas contra los textos primarios antes de una publicación externa.*
</content>
