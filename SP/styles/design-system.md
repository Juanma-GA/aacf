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

# Framework de Diseño UX Unificado

Un único lenguaje de diseño para **todos** los proyectos de vibe-coding de ATEXIS, para que las UIs
generadas por IA sean consistentes por construcción — no aleatorias. El principio tomado del estado
del arte (`../docs/STATE_OF_THE_ART.md`): **la consistencia proviene de hacer del sistema de diseño un
artefacto instalable compartido + una instrucción de agente compartida, no de esperar que cada
generación "encaje con el estilo."**

El sistema tiene **tres capas**, cada una un artefacto real que los proyectos consumen:

```
   ┌──────────────────────────────────────────────────────────────┐
   │  Capa 3 — Capa de AGENTE (reglas + Skill + registro MCP)      │  cómo la IA lo sigue
   ├──────────────────────────────────────────────────────────────┤
   │  Capa 2 — Capa de COMPONENTES (preset shadcn registry:base)   │  el punto de partida de referencia
   ├──────────────────────────────────────────────────────────────┤
   │  Capa 1 — Capa de TOKENS (DTCG *.tokens.json → vars CSS)      │  la única fuente de verdad
   └──────────────────────────────────────────────────────────────┘
```

## Capa 1 — Tokens (la única fuente de verdad)

- Redacta **un único** fichero de tokens de diseño en el formato **W3C Design Tokens (DTCG)** (primera
  especificación estable 2025.10): `design.tokens.json`. Colores en **OKLCH**, más espaciado, escala
  tipográfica, radios, movimiento, y temas claro/oscuro + de marca. Propiedades con prefijo `$`,
  alias/herencia.
- Compílalo con **Style Dictionary** en `globals.css` como **propiedades personalizadas CSS** + el
  tema de Tailwind. Nada hardcodea un valor hexadecimal o un valor px — los componentes referencian
  **variables de token semánticas** (`--color-primary`, `--radius`, `--space-md`), nunca valores en
  crudo. Esta es la expresión en el sistema de diseño de **HR0/HR8 (sin hardcoding, todo
  configurable)**.
- Los valores canónicos viven en [`branding.md`](branding.md) (azul ATEXIS `#2E74B5`, Inter /
  JetBrains Mono, escala de espaciado/radio, colores semánticos + por nivel). Trata ese fichero como
  el espejo legible por humanos de la fuente de tokens.

## Capa 2 — Componentes (el punto de partida de referencia)

- Estandariza en **shadcn/ui + Tailwind** — el código vive *en el proyecto* (no una dependencia de
  caja negra), para que el agente pueda leerlo, comprenderlo y extenderlo. Por eso v0 y la mayoría de
  las herramientas de IA lo usan por defecto.
- Entrega los componentes de ATEXIS como un **preset privado `registry:base` de shadcn** (CLI v4).
  Entonces el camino de referencia para un proyecto nuevo es un único comando:

  ```bash
  npx shadcn init <atexis-registry>     # instala tokens, fuentes, configuración, componentes verificados
  ```

  Cada proyecto empieza **idéntico por construcción** — mismos tokens, mismas primitivas, misma
  configuración.
- Las convenciones de componentes (botones, formularios, tablas, navegación, tarjetas, diálogos,
  toasts) están en [`ui-kit.md`](ui-kit.md). Compón a partir del registro; **nunca fabriques a mano
  una primitiva** que ya existe.

## Capa 3 — Agente (cómo la IA lo sigue)

- Provee una **regla/Skill de sistema de diseño** que: detecte `components.json`, lea la
  configuración real del proyecto (`shadcn info --json` — framework, versión de Tailwind, alias,
  biblioteca de iconos, componentes instalados), y **aplique las reglas de composición** antes de
  generar (p. ej. usar `FieldGroup` para formularios, `ToggleGroup` para conjuntos de opciones, solo
  colores semánticos).
- Expón el registro privado sobre un **servidor MCP** para que el agente instale desde una *paleta
  restringida* en lugar de inventar marcado.
- Los criterios de aceptación no negociables del agente para cualquier UI que produzca:
  - **Referenciar variables de token semánticas, nunca hexadecimal/px en crudo.**
  - **WCAG 2.2 AA** — texto alternativo significativo (no un marcador de posición), contraste
    suficiente, foco de teclado visible, etiquetas reales.
  - **Respetar `prefers-reduced-motion`**; el contenido en movimiento automático de más de 5s se
    puede pausar (WCAG 2.2.2).
  - **Responsive hasta móvil.**
  - **Humanizar todo el texto mostrado (HR21)** — nunca renderizar `snake_case` / claves de enum /
    slugs de estado; reemplazar separadores, capitalizar (title-case), formatear listas de forma
    legible.
  - **Localizar todo el texto de la UI (HR15)** vía la capa de i18n.
  - **UI de mutación optimista (HR20)** — reflejar de inmediato, revertir si el servidor rechaza.

## El contrato y la puerta de lanzamiento

- Un **Storybook** es el contrato humano + de regresión visual para los componentes.
- CI ejecuta una **comprobación de accesibilidad** (axe / WCAG 2.2 AA) y regresión visual como
  **puerta de lanzamiento** — una UI que falla la a11y o se desvía del sistema de tokens no se
  despliega.

## Por qué esta forma

- **Tokens como datos** → un cambio retematiza cada proyecto; sin deriva de hexadecimales por
  proyecto.
- **Componentes como un preset instalable** → los proyectos nuevos empiezan consistentes, no desde un
  lienzo en blanco.
- **Sistema de diseño como instrucción de agente + paleta MCP restringida** → la IA produce código
  accesible y de marca al primer intento en lugar de marcado verosímil pero aleatorio.

*Ver [`branding.md`](branding.md) para los valores de token y [`ui-kit.md`](ui-kit.md) para las
convenciones de componentes. Investigación y fuentes:
[`../docs/STATE_OF_THE_ART.md`](../docs/STATE_OF_THE_ART.md).*
</content>
