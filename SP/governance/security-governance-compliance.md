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

# Controles de Seguridad, Gobernanza y Cumplimiento

El catálogo de controles para el desarrollo asistido por IA / "vibe coding" en ATEXIS. Basado en
**ISO/IEC 27001:2022**, **GDPR**, un **Ciclo de Vida de Desarrollo de Software Seguro** (OWASP web +
LLM Top 10, NIST SSDF/800-218A, SLSA), y la **EU AI Act**. Estado del arte a mediados de 2026
(`../docs/STATE_OF_THE_ART.md`).

Cada control indica **qué**, **por qué**, y **la cláusula a la que se mapea**. Los mecanismos de
aplicación (hooks, puertas, escaneo) están en [`guardrails.md`](guardrails.md). Cada control aquí es
comprobado por el agente [`security-reviewer`](../agents/security-reviewer.agent.md) y, para
producción, por el agente [`codebase-hardening`](../agents/codebase-hardening.agent.md).

> **Nota de alcance.** Los asistentes internos de codificación de ATEXIS hacen de ATEXIS un
> *implementador/productor* de sistemas de IA. Bajo el Digital Omnibus de la EU AI Act, el plazo de
> cumplimiento para alto riesgo se aplaza al **2 de diciembre de 2027**, pero las obligaciones de
> alfabetización en IA (Art. 4) y prácticas prohibidas (Art. 5) ya aplican, y las obligaciones de
> ISO/GDPR vinculan ahora. Los *entregables* al cliente se rigen por el propio contrato del cliente +
> ISO 27001 + GDPR (no la política interna de ATEXIS); todo lo demás sigue el modelo por niveles en
> [`tier-checklists.md`](tier-checklists.md).

---

## 1. OWASP — riesgos específicos del código generado por IA

**OWASP Top 10 para Aplicaciones LLM (2025):** LLM01 Inyección de Prompts · LLM02 Divulgación de
Información Sensible · LLM03 Cadena de Suministro · LLM04 Envenenamiento de Datos y Modelos · LLM05
Manejo Inadecuado de la Salida · LLM06 Agencia Excesiva · LLM07 Filtración del Prompt de Sistema ·
LLM08 Debilidades de Vectores e Incrustaciones (Embeddings) · LLM09 Desinformación · LLM10 Consumo
Ilimitado.

| # | Control | Por qué | Se mapea a |
|---|---------|-----|---------|
| 1.1 | Tratar toda salida de IA como no confiable — nunca ejecutar/`eval`/shell/renderizar sin validación, codificación, sandboxing. | La inyección de prompts se encadena hacia el manejo de la salida: las instrucciones inyectadas se convierten en código que ejecuta la aplicación. | LLM05; OWASP ASVS V1 |
| 1.2 | Separar las instrucciones de los datos en los prompts; prompts estructurados/parametrizados + delimitadores; sin secretos en los prompts de sistema. | El procesamiento de un solo canal es la raíz de la inyección y la filtración de prompts. | LLM01, LLM07 |
| 1.3 | Verificar que cada paquete sugerido por IA existe y es anterior al proyecto; bloquear paquetes recién registrados (periodo de espera de 30–90 días). | "Slopsquatting": ~19,7% del código de IA alucina nombres de paquetes; ~43% se repiten, por lo que los atacantes los pre-registran. | LLM03 |
| 1.4 | Lista blanca de dependencias + fijación de lockfile/hash; cualquier otra cosa → revisión humana. | Elimina el punto ciego de los paquetes; builds deterministas. | LLM03; ISO A.5.21; SLSA |
| 1.5 | Restringir la agencia del agente — tokens de mínimo privilegio, sin acciones destructivas/de producción sin aprobación. | La agencia excesiva convierte una mala sugerencia en un incidente. | LLM06 |
| 1.6 | Escanear cada diff de IA en busca de secretos antes de que se aplique; nunca incrustar credenciales. | Los asistentes tanto filtran como hardcodean secretos. | LLM02; ASVS V13 |
| 1.7 | Contrarrestar la dependencia excesiva — revisión humana obligatoria, etiquetar los commits de IA, el autor debe explicar el código, verificar las APIs. | Los modelos emiten código verosímil pero incorrecto y APIs inventadas. | LLM09 |
| 1.8 | Limitar la tasa y aplicar cuotas a las herramientas de IA; monitorizar tokens/coste por usuario y repositorio. | Coste desbocado / DoS vía bucles de agente. | LLM10 |
| 1.9 | Aplicar **OWASP ASVS 5.0** como el umbral de aceptación (L1 público no sensible, **L2 por defecto**, L3 impacto catastrófico). | ASVS es el checklist normativo que detecta los defectos de inyección/autorización/criptografía que reintroduce la IA. | ASVS 5.0 (V1–V14) |
| 1.10 | Mapear cada funcionalidad web generada por IA al **OWASP Top 10 (web)** — especialmente A01, A03, A08. | Los asistentes replican fallos web clásicos. | OWASP Top 10 |

---

## 2. SDLC Seguro (NIST SSDF / 800-218A, SLSA, Microsoft SDL)

| # | Control | Por qué | Se mapea a |
|---|---------|-----|---------|
| 2.1 | Adoptar **NIST SSDF (SP 800-218)** como columna vertebral — grupos de práctica PO / PS / PW / RV. | Línea base auditable reconocida; cada control de abajo depende de ella. | SSDF v1.1 |
| 2.2 | Superponer **NIST SP 800-218A** (Desarrollo Seguro para IA Generativa) para cualquier componente de IA construido/afinado/adquirido. | El complemento de SSDLC específico de IA: procedencia de modelo/datos, integridad del entrenamiento, manejo de abuso. | SP 800-218A |
| 2.3 | **SAST en cada PR** (y pre-fusión en diffs de IA). | Detecta defectos de inyección/autorización antes de fusionar. | SSDF PW.7/8; ISO A.8.29 |
| 2.4 | **SCA + SBOM** como paso obligatorio de CI; verificar dependencias contra hashes conocidos-buenos. | Detecta dependencias vulnerables/alucinadas/maliciosas; el SBOM es la procedencia que esperan los auditores. | SSDF PS.3/PW.4; SLSA; ISO A.5.21 |
| 2.5 | **DAST** en staging antes del lanzamiento. | Validación en tiempo de ejecución de la aplicación desplegada. | SSDF PW.8; ISO A.8.29 |
| 2.6 | **Escaneo de secretos en tres puertas** — pre-commit, PR, CI. | Defensa en profundidad contra la fuga de credenciales por IA; se dispara sin importar el autor. | SSDF PS.1; ISO A.8.24 |
| 2.7 | Adoptar la **procedencia de build SLSA** — Build **L2 mínimo**, L3 para artefactos de alto valor; verificar con `slsa-verifier`; adoptar la pista de Source. | Atestación firmada y resistente a manipulación de cómo/dónde se construyó cada artefacto. | SLSA v1.2 |
| 2.8 | Aplicar el pilar de modelado de amenazas de **Microsoft SDL "for AI"** — modelos de amenazas de IA, observabilidad, identidad de agente más fuerte. | Complemento del profesional que extiende el desarrollo seguro a los agentes. | Microsoft SDL |
| 2.9 | **Modelar las amenazas de las funcionalidades de IA antes de construir**; remodelar cuando cambien las capacidades del agente. | El ámbito de herramientas del agente es una nueva superficie de ataque. | SSDF PW.1 |
| 2.10 | **Respuesta a vulnerabilidades (RV)** cubriendo defectos introducidos por IA e incidentes de dependencias alucinadas. | El slopsquatting es una amenaza externa continua, no un escaneo puntual. | SSDF RV.1–3 |

---

## 3. ISO/IEC 27001:2022 Anexo A (+ ISO/IEC 42001)

**Controles de desarrollo seguro A.8.25–A.8.34** (títulos exactos):

| Cláusula | Título | Aplicado a la plataforma de codificación con IA |
|--------|-------|-----------------------------------|
| A.8.25 | Ciclo de Vida de Desarrollo Seguro | Reglas de desarrollo seguro documentadas que cubren la autoría asistida por IA y el uso de agentes. |
| A.8.26 | Requisitos de Seguridad de Aplicaciones | Requisitos de seguridad (incl. manejo de la salida de IA) definidos antes de construir; el nivel ASVS se fija aquí. |
| A.8.27 | Principios de Arquitectura e Ingeniería de Sistemas Seguros | Agentes en sandbox, tokens de mínimo privilegio, límites de confianza alrededor de la salida de IA. |
| A.8.28 | Codificación Segura | Los estándares de codificación segura aplican a la salida de IA de forma idéntica a la salida humana. |
| A.8.29 | Pruebas de Seguridad en Desarrollo y Aceptación | Las puertas de SAST/DAST/SCA (2.3–2.5) son la evidencia. |
| A.8.30 | Desarrollo Externalizado | Rige a los proveedores/modelos externos de codificación con IA como desarrollo externalizado. |
| A.8.31 | Separación de Dev / Test / Producción | Los agentes nunca tocan producción; protección de ramas en `main`; credenciales por entorno. |
| A.8.32 | Gestión de Cambios | Los cambios redactados por IA pasan por los mismos controles de cambio + puertas de revisión. |
| A.8.33 | Información de Pruebas | Sin datos personales reales en prompts/fixtures de prueba de IA (→ GDPR §4). |
| A.8.34 | Protección Durante las Pruebas de Auditoría | Acota el acceso de agente/escáner durante las auditorías. |

**Controles de apoyo:** A.5.15 Control de Acceso (RBAC + mínimo privilegio en la plataforma, repositorios,
identidades de agente) · A.8.15 Registro (todas las acciones de IA registradas centralmente en el
SIEM) · A.8.24 Criptografía (cifrar en tránsito/en reposo; secretos en una bóveda, nunca en
prompts/código) · A.5.19–A.5.23 Gobernanza de proveedores/cadena de suministro de TIC (DPAs,
retención, revisión de cambio de proveedor para proveedores de modelos y registros).

**ISO/IEC 42001:2023 (Sistema de Gestión de IA)** — el paraguas que vincula los controles específicos
de IA al SGSI de ISO 27001 existente (PDCA AIMS, 9 objetivos / 38 controles del Anexo A). Su control de
**evaluación de impacto de IA** unifica la DPIA de GDPR y el artefacto de gestión de riesgos de la EU
AI Act en uno solo.

---

## 4. GDPR — desarrollo asistido por IA que maneja datos personales

| # | Control | Por qué | Cláusula |
|---|---------|-----|--------|
| 4.1 | **No enviar datos personales a modelos externos.** Denegación por defecto de datos personales/confidenciales en los prompts; DLP en la salida; preferir modelos autoalojados; vincular términos de no-entrenamiento/retención en el DPA donde el uso externo sea inevitable. | Enviar datos personales a un LLM de terceros es un evento de procesamiento + potencial transferencia con riesgo de reutilización para entrenamiento. | Art. 5, Art. 28, Cap. V |
| 4.2 | **Minimización de datos** — los prompts/fixtures llevan solo los datos necesarios; pseudonimizar/anonimizar antes del procesamiento por IA; eliminar al terminar. | Deber central del Art. 5(1)(c). | Art. 5(1)(c) |
| 4.3 | **DPIA antes de desplegar IA contra datos personales** — tratar IA-sobre-datos-personales como un disparador presuntivo de alto riesgo. | El Art. 35 requiere una DPIA para el procesamiento de alto riesgo; el LLM-sobre-datos-personales usualmente cruza el umbral. | Art. 35 |
| 4.4 | **Limitación de propósito** — los datos recopilados para el producto X no pueden reutilizarse para dar prompts/entrenar herramientas sin una nueva base legal. | Art. 5(1)(b). | Art. 5(1)(b) |
| 4.5 | El **RoPA** incluye el procesamiento de desarrollo asistido por IA y las transferencias a modelos externos. | Registro del Art. 30; también alimenta la clasificación de la AI Act. | Art. 30 |
| 4.6 | **Los derechos del interesado se mantienen respetados** — no dejar que los datos personales queden irrecuperablemente incrustados en un modelo o en los logs de un proveedor. | Incrustar en un modelo externo puede hacer imposible la eliminación. | Arts. 15–22 |
| 4.7 | Los **acuerdos con proveedores** fijan la retención de entrada/salida y prohíben entrenar con tus datos. | Controla el riesgo residual del uso externo permitido. | Art. 28 |

---

## 5. EU AI Act — obligaciones prácticas para herramientas internas de IA

| # | Control | Por qué | Cláusula |
|---|---------|-----|--------|
| 5.1 | **Inventariar y clasificar cada sistema de IA** — inaceptable / alto riesgo / limitado / mínimo. El asistente de desarrollo suele ser limitado/mínimo; cualquier cosa usada para **decisiones de empleo** es de alto riesgo (Anexo III). | Las obligaciones escalan por nivel; clasificar mal una herramienta relacionada con RRHH es la trampa común. | Art. 6 + Anexo III |
| 5.2 | **Registrar la clasificación** — metodología, criterios, evidencia, hoja de ruta. | Diligencia debida demostrable; rastro auditable. | Art. 6 |
| 5.3 | **Alfabetización en IA para el personal que usa herramientas de IA** — en vigor desde febrero de 2025. | El Art. 4 aplica ya, independientemente del aplazamiento de alto riesgo. | Art. 4 |
| 5.4 | **Comprobar contra prácticas prohibidas** — sin puntuación social / manipulación / uso biométrico ilícito. | Las prohibiciones del Art. 5 aplican ya, con las sanciones más severas. | Art. 5 |
| 5.5 | **Si algún sistema es de alto riesgo, construir el paquete completo** — gestión de riesgos, gobernanza de datos, documentación técnica, registro, supervisión humana, precisión/robustez, evaluación de conformidad, registro en la base de datos de la UE. Plazo **2 de diciembre de 2027**; construye la hoja de ruta ahora. | Los deberes de alto riesgo son extensos; el aplazamiento compra tiempo de preparación, no exención. | régimen de alto riesgo |
| 5.6 | **Transparencia para riesgo limitado** — etiquetar las sugerencias/salidas redactadas por IA; los usuarios saben que interactúan con IA. | Deber de transparencia del Art. 50. | Art. 50 |

---

## 6. Mapeo cruzado de estándares — una implementación, muchas auditorías

| Implementación | OWASP | NIST SSDF/218A | ISO 27001/42001 | GDPR | EU AI Act |
|---|---|---|---|---|---|
| SAST/DAST/SCA + SBOM en CI | LLM03, ASVS | PW.7/8, PS.3 | A.8.29, A.5.21 | — | gobernanza de datos de alto riesgo |
| Escaneo de secretos (3 puertas) | LLM02 | PS.1 | A.8.24 | Art. 32 | — |
| Lista blanca de dependencias + periodo de espera | LLM03 | PW.4 | A.5.21 | — | cadena de suministro |
| Protección de ramas / sin agente→`main` | LLM06 | PW.4 | A.8.31/32 | — | — |
| Puerta de revisión humana | LLM06/09 | PW.7 | A.8.32 | Art. 22 | supervisión humana |
| Registro de auditoría en SIEM | LLM06 | RV.1 | A.8.15, 42001 | Art. 30 | registro/trazabilidad |
| DLP / sin datos personales a modelos externos | LLM02 | PS.1 | A.8.24 | Art. 5/28, Cap. V | — |
| DPIA / evaluación de impacto de IA | — | — | control de impacto 42001 | Art. 35 | gestión de riesgos |

---

## Prioridades para ATEXIS

- **El slopsquatting es el nuevo riesgo de mayor señal** para el vibe coding — los controles 1.3, 1.4,
  2.4 y las barreras de cadena de suministro son baratos, deterministas, y van primero.
- **Hacer que las barreras no sean eludibles** — hooks deterministas, puertas duras de escaneo de
  secretos, protección de ramas, registro de auditoría completo. Los agentes pueden racionalizar en
  torno a la prosa consultiva pero no pueden pasar por alto un hook que falla (ver
  [`guardrails.md`](guardrails.md)).
- **ISO/IEC 42001 es el paraguas** para vincular estos controles de IA al SGSI de ISO 27001 existente;
  su control de evaluación de impacto unifica la DPIA de GDPR + la gestión de riesgos de la AI Act.

*Fuentes y citas completas: [`../docs/STATE_OF_THE_ART.md`](../docs/STATE_OF_THE_ART.md). Algunas
cifras de mediados de 2026 (tasas de alucinación, la fecha de aplazamiento del Digital Omnibus)
provienen de reportes secundarios — verifícalas contra los textos primarios de NIST / el Diario
Oficial antes de una publicación externa.*
</content>
