---
description: ATEXIS Hard Rules (HR0–HR21) — non-negotiable engineering constraints for all AI-assisted development
globs: **/*
alwaysApply: true
---

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


# Reglas Estrictas de ATEXIS (HR0–HR21)

Estas son las restricciones **absolutas, no negociables** para todo proyecto asistido por IA ("vibe
coding") en ATEXIS. Son la fuente de las barreras que aplica el AACF. Los agentes de codificación con
IA, los desarrolladores humanos, y los revisores están sujetos a ellas por igual. Un cambio que viola
una Regla Estricta no está "hecho" — es un defecto.

> **Cómo leer esto:** cada regla es imperativa ("debe"/"nunca"), una preocupación por regla, y
> redactada de forma que se pueda comprobar. Cuando un agente propone código, se autocomprueba contra
> esta lista antes de presentarlo; los revisores comprueban contra ella antes de aprobar (ver
> `../governance/code-review-guidelines.md`).

## Datos y autenticación

- **HR0 — La configuración es una superficie de primera clase.** Cada ajuste de YAML/configuración
  debe tener una sección correspondiente en la UI de administración. Nada ajustable se esconde en el
  código.
- **HR1 — Sin almacenamiento del navegador para el estado.** Nunca uses `localStorage` /
  `sessionStorage` para estado autoritativo o persistente. El servidor (Postgres) es la fuente de
  verdad.
- **HR8 — Sin hardcoding.** Todo configurable — endpoints, umbrales, límites, feature flags, textos.
  Sin números mágicos ni cadenas mágicas en la lógica.
- **HR9 — Nunca elimines datos persistentes sin confirmación explícita.** Eliminación suave
  (soft-delete) / retención por defecto; las operaciones destructivas requieren una confirmación
  explícita y registrada.
- **HR10 — Fusión profunda (deep-merge) de configuración anidada, nunca fusión superficial.** Usa
  `deep_merge()`; una fusión superficial pierde claves anidadas en silencio.
- **HR15 — Todo el texto de la UI debe ser localizable.** Enruta las cadenas de cara al usuario a
  través de la capa de i18n (p. ej. `next-intl`); sin cadenas de visualización hardcodeadas.
- **HR21 — Humaniza todo el texto de cara al usuario.** Nunca renderices tokens de máquina en crudo
  (snake_case, kebab-case, claves de enum, slugs de estado) en la UI. Humaniza antes de mostrar —
  reemplaza los separadores, capitaliza (title-case), formatea las listas de forma legible. Aplica a
  cada superficie: tarjetas, tablas, vistas de detalle, notificaciones, correos electrónicos.

## Calidad de código

- **HR2 — Corrige todos los problemas detectados, y prueba cada corrección.** Ningún código
  conocido-roto se despliega.
- **HR3 — Verifica contra el código base real.** Fundamenta cada afirmación en ficheros reales;
  proporciona referencias (`path:line`). Sin afirmaciones sobre código que no has leído.
- **HR4 — Nunca simplifiques en silencio.** Si la tarea no se puede realizar según lo especificado,
  reporta el bloqueo y escala — no despliegues silenciosamente una versión reducida.
- **HR5 — Cada cambio de código debe desplegarse** (vía el procedimiento de despliegue del proyecto,
  nunca copiando ficheros a mano).
- **HR6 — Sin truncado de contenido.** Nunca recortes contenido de calidad o de cara al usuario
  (documentación, historial, respuestas) para ajustarse a un límite — usa resumen con LLM en su
  lugar. Acotar una señal de control consumida por máquina (la salida estructurada de un
  calificador/juez) está permitido solo cuando es consumida por código, comprimida por el propio LLM
  (decodificación guiada / brevedad instruida, nunca recorte), y no pierde nada de fidelidad para la
  decisión. Prefiere JSON guiado antes que límites de `max_tokens`.
- **HR7 — Sin fallbacks que degraden la UX, la calidad, o la integridad de datos.** Un fallback
  silencioso con pérdida es peor que un fallo visible. Escala en su lugar.
- **HR11 — Sin expresiones regulares para operaciones críticas.** Para decisiones
  semánticas/críticas usa una llamada a un LLM, no un patrón frágil.
- **HR13 — Sin código legado.** Elimina o refactoriza el código muerto/duplicado; no lo dejes "por si
  acaso."
- **HR14 — Modelo de desarrollador único.** Sin estimaciones de esfuerzo/tiempo; haz el trabajo.
- **HR16 — Verifica que no hay otro despliegue en curso antes de iniciar uno.**
- **HR18 / HR19 — Sin timeouts fijos en procesos agénticos.** Sin `max_iterations`, sin bucles de
  `time.sleep()`, sin límites duros silenciosos. Usa un watchdog + escalado para cualquier límite,
  nunca un rechazo silencioso.
- **HR20 — Toda UI de mutación debe ser OPTIMISTA.** Refleja el cambio de inmediato, persiste en
  segundo plano, revierte solo si el servidor rechaza. La única excepción es un resultado
  genuinamente calculado por el servidor / impredecible (consolidación por LLM, id generado por el
  servidor) — entonces aplica la respuesta autoritativa y documenta por qué.

## Flujos de trabajo agénticos

- Sin `max_iterations`, sin bucles de `time.sleep()`, sin límites duros (HR18/HR19).
- Sin truncado — resume con un LLM (HR6).
- Escala en lugar de recurrir a un fallback (HR7).
- Watchdog + escalado para cualquier límite; nunca rechazo silencioso.

## Autenticación y almacenamiento

- Postgres es la fuente de verdad.
- Autenticación vía cookies HTTP-only (establecidas por el servidor), nunca un JWT en
  `localStorage`.
- Sin estado autoritativo sin conexión.

## Escollos de lenguaje / framework

- **Python / FastAPI:** NUNCA pongas `from __future__ import annotations` en un fichero de rutas
  FastAPI — rompe `include_router()` en silencio. Python 3.12 soporta nativamente `str | None` y
  `dict[str, Any]`, así que de todos modos es innecesario.

## Antipatrones (nunca despliegues esto)

- `localStorage` / `sessionStorage` para el estado (HR1)
- `from __future__ import annotations` en ficheros de rutas
- Valores de configuración hardcodeados (HR8)
- Expresiones regulares para decisiones críticas/semánticas (HR11)
- Fusiones superficiales de diccionarios (HR10)
- Lógica agéntica con timeout / bucle de `sleep` (HR18/HR19)
- Truncar contenido para ajustarse a una ventana en lugar de resumir (HR6)
- Bloquear una UI de mutación en un round-trip cuando el cliente ya conoce el resultado (HR20)
- Tokens de máquina en crudo renderizados en la UI (HR21)

---

*Estas Reglas Estrictas son la capa específica de ATEXIS sobre las reglas globales del AACF
(`global_rules.md`), las reglas de seguridad (`security.mdc`), y las reglas de seguridad de salida de
IA (`ai-output-safety.mdc`). Donde una regla general y una Regla Estricta se solapen, la Regla
Estricta prevalece.*
</content>
