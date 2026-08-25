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

# ATEXIS AI Framework — Instrucciones de Vibe Coding

Coloca este fichero en tu proyecto (por ejemplo, guárdalo como `AGENTS.md`, o pégalo en las
reglas/prompt de sistema de tu asistente). Le indica a tu agente de codificación con IA qué es el
ATEXIS AI Framework (AACF), cómo obtener contenido aprobado bajo demanda vía la herramienta MCP
`aacf_fetch`, y las reglas no negociables que todo proyecto de ATEXIS debe seguir.

> **Estás programando en ATEXIS.** Construye sobre las plantillas, reglas, estilos y gobernanza
> aprobados del AACF en lugar de empezar desde cero. Ante la duda, obtén el documento AACF
> correspondiente y síguelo.

---

## 1. Obtener contenido de AACF con la herramienta MCP `aacf_fetch`

La plataforma de IA de ATEXIS sirve el AACF en modo de solo lectura a través de una herramienta MCP
llamada **`aacf_fetch`**, alojada en el servidor MCP de la plataforma. Úsala para leer cualquier
documento del framework en el momento en que lo necesites — no adivines el contenido, obténlo.

- **Endpoint:** `http://10.117.139.1:8200/mcp`  (transporte: **streamable-http**)
- **Autenticación:** `Authorization: Bearer <TU_CLAVE>`  *(o la cabecera `X-Api-Key: <TU_CLAVE>`)*
- **Clave:** obtén una en **Consola de Administración → Claves API** (se muestra una única vez). Solo red corporativa / VPN.

**Herramienta:** `aacf_fetch`

| Parámetro | Tipo | Descripción |
|---|---|---|
| `path` | string (obligatorio) | Ruta dentro del AACF, p. ej. `rules/python.mdc`, `templates/web-app.md`. Usa `""` con `list_dir:true` para listar la raíz |
| `list_dir` | boolean (por defecto false) | Lista las entradas de un directorio en lugar de leer un fichero |
| `include_version` | boolean (por defecto false) | Antepone la versión de AACF a la respuesta |

**Ejemplos**

```jsonc
// Descubrir qué está disponible
aacf_fetch({ "path": "", "list_dir": true, "include_version": true })

// Leer las reglas estrictas antes de escribir cualquier código
aacf_fetch({ "path": "rules/atexis-hard-rules.md" })

// Obtener el punto de partida aprobado para el tipo de proyecto que estás construyendo
aacf_fetch({ "path": "templates/web-app.md" })

// Reglas de lenguaje + seguridad
aacf_fetch({ "path": "rules/python.mdc" })
aacf_fetch({ "path": "rules/ai-output-safety.mdc" })

// El sistema de diseño unificado (para que cada UI de ATEXIS luzca consistente)
aacf_fetch({ "path": "styles/design-system.md" })
```

### Conectar tu agente al servidor MCP

**VS Code (`.vscode/mcp.json`):**

```json
{
  "servers": {
    "atexis-aacf": {
      "type": "http",
      "url": "http://10.117.139.1:8200/mcp",
      "headers": { "Authorization": "Bearer ${input:atexisKey}" }
    }
  },
  "inputs": [
    { "id": "atexisKey", "type": "promptString", "description": "Clave API de ATEXIS", "password": true }
  ]
}
```

**Claude Code (CLI):**

```bash
claude mcp add atexis-aacf --transport http http://10.117.139.1:8200/mcp \
  -H "Authorization: Bearer sk-...TU_CLAVE..."
```

**Continue (`~/.continue/config.json`):**

```jsonc
{
  "experimental": {
    "modelContextProtocolServers": [
      { "transport": { "type": "streamable-http",
        "url": "http://10.117.139.1:8200/mcp",
        "requestOptions": { "headers": { "Authorization": "Bearer sk-...TU_CLAVE..." } } } }
    ]
  }
}
```

**Prueba de humo (curl):**

```bash
API=sk-...TU_CLAVE...
curl -s http://10.117.139.1:8200/mcp \
  -H "Authorization: Bearer $API" \
  -H "Accept: application/json, text/event-stream" -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"aacf_fetch","arguments":{"path":"rules/atexis-hard-rules.md"}}}'
```

Todo el tráfico permanece en la red interna de ATEXIS — sin llamadas externas, sin telemetría.

> El mismo endpoint MCP también expone las herramientas RAG de la plataforma (`rag_search`, `rag_query`, …)
> para recuperación sobre corpus aprobados. Otros servidores MCP (investigación, transcripción, …) están
> detrás de la pasarela en `https://172.21.28.81/mcp/<name>/mcp` — ver la Guía de Conexión de la plataforma.

### Frescura de versión — verifica antes de confiar (una vez por sesión)

El contenido que obtienes en vivo vía `aacf_fetch` es siempre la versión desplegada del framework. Pero
si estás trabajando desde una **copia local** de cualquier fichero de AACF (el ZIP del framework, ficheros
comiteados en un repositorio):

1. `aacf_fetch({"path": "VERSION"})` y compárala con el `aacf/VERSION` local.
2. **¿Diferente?** Tu copia está desactualizada — vuelve a obtener TODOS los ficheros del framework vía
   `aacf_fetch` (recorre desde `{"path": "", "list_dir": true}`), o pide al usuario que vuelva a descargar
   el ZIP del framework desde el portal IdAI (`/propose/framework`). Nunca mezcles ficheros de distintas
   versiones del framework.
3. **¿No puedes acceder al MCP?** Dile al usuario: *no puedes acceder al MCP de AACF y podrías estar
   trabajando con una versión desactualizada del framework* — luego continúa con la copia local.

Cada fichero del ZIP del framework descargado lleva este protocolo como comentario de encabezado,
marcado con la versión exacta de la instantánea en el momento de la descarga.

---

## 2. El mapa — qué obtener, y cuándo

| Estás… | Obtén |
|---|---|
| Empezando un proyecto nuevo | `templates/` → elige el punto de partida más cercano (`web-app`, `api-service`, `data-pipeline`, `internal-tool`, `automation-script`) |
| Escribiendo código | `rules/atexis-hard-rules.md`, luego la regla de lenguaje (`rules/python.mdc`, `rules/javascript.mdc`, `rules/dotnet.mdc`) |
| Generando código con IA | `rules/ai-output-safety.mdc` (OWASP LLM Top 10, cadena de suministro / slopsquatting, inyección de prompts) |
| Construyendo cualquier UI | `styles/design-system.md`, `styles/branding.md` |
| Preparándolo para revisión o auditoría | `governance/security-governance-compliance.md`, `governance/guardrails.md`, `governance/tier-checklists.md` |
| Escribiendo prompts / agentes | `prompts/prompt-library.md` |

Explora el framework completo en la aplicación en **`/propose/framework`** (Mis propuestas → Framework).

---

## 3. Las Reglas Estrictas (HR0–HR21) — siempre vigentes

Obtén `rules/atexis-hard-rules.md` para el texto autorizado. Resumen:

- **HR0** Todo valor de configuración se expone en una UI de administración. **HR8** Nada hardcodeado — todo configurable.
- **HR1** Sin `localStorage` / `sessionStorage` para autenticación o estado. La autenticación es vía cookies HTTP-only; Postgres es la fuente de verdad.
- **HR2** Corrige cada problema que detectes y prueba la corrección. **HR3** Verifica contra el código real, cita referencias.
- **HR4** Si una tarea no se puede hacer correctamente, reporta el bloqueo — no simplifiques en silencio.
- **HR6** Nunca truncar contenido de calidad o de cara al usuario — resume con un LLM en lugar de recortar.
- **HR7** Sin fallbacks que degraden silenciosamente la UX, la calidad o la integridad de los datos — escala en su lugar.
- **HR9** Nunca elimines datos persistentes/de usuario sin confirmación explícita.
- **HR10** Fusión profunda (deep-merge) de configuración anidada; nunca fusión superficial. **HR13** Sin código legado — elimínalo o refactorízalo.
- **HR11** No uses expresiones regulares para operaciones críticas/semánticas — usa una llamada a un LLM.
- **HR15** Todo el texto de la UI es localizable. **HR21** Nunca renderices tokens de máquina en crudo (snake_case, claves de enum) — humaniza antes de mostrar.
- **HR18/19** Sin timeouts fijos ni límites de iteración en trabajo agéntico — usa un watchdog + escalado.
- **HR20** Toda UI de mutación es optimista — refleja el cambio de inmediato, persiste en segundo plano, revierte solo si el servidor rechaza.

---

## 4. El camino de referencia

1. **Obtén** el punto de partida de `templates/` más cercano y construye sobre él.
2. **Obtén** las reglas estrictas + tu regla de lenguaje; síguelas mientras escribes.
3. Si estás generando código con IA, **obtén** `rules/ai-output-safety.mdc` y respétalo (verifica que cada dependencia existe, nunca inventes paquetes, nunca vuelques entrada no confiable en shell/SQL/HTML).
4. Para UI, **obtén** el sistema de diseño y usa los tokens y componentes aprobados.
5. Antes de darlo por terminado, **obtén** las barreras de gobernanza + el checklist del nivel de tu proyecto y autocomprúebate contra ellos.

Construye sobre componentes aprobados. Obtén, no adivines. Sigue las reglas estrictas.
</content>
