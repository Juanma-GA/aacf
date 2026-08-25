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

# Checklists de Gobernanza por Nivel

## T1 — Autoservicio (Herramientas de IDE)

### Antes del uso
- [ ] La herramienta está en el Registro de Herramientas de IA aprobado
- [ ] El usuario ha completado el módulo de formación requerido
- [ ] La herramienta opera en modo aislado de red (sin acceso a internet)
- [ ] No se procesan datos confidenciales

### Durante el uso
- [ ] El código generado respeta las reglas globales de AACF
- [ ] No se pasan datos sensibles como prompts
- [ ] Las salidas se revisan antes de hacer commit

### Cumplimiento
- [ ] No se requiere revisión adicional
- [ ] Uso registrado vía telemetría del IDE

---

## T2 — Aprobado por IS (Herramientas Internas)

### Antes del despliegue
- [ ] Iniciativa registrada en IdAI con justificación de negocio
- [ ] Revisión de IS completada y aprobada
- [ ] La evaluación de riesgo la clasifica como BAJA o ESTÁNDAR
- [ ] Formación completada para todos los usuarios previstos
- [ ] Herramienta registrada en el Registro de Herramientas de IA

### Durante la operación
- [ ] Ejecutándose en VM compartida (aprovisionada por TI)
- [ ] Registro de auditoría activo
- [ ] Limitación de tasa configurada
- [ ] Clasificación de datos: solo Interno o inferior
- [ ] Revisión mensual de uso programada

### Cumplimiento
- [ ] Controles de ISO 27001 mapeados
- [ ] GDPR: sin datos personales sin DPIA
- [ ] Revisión trimestral señalada en IdAI

---

## T3 — IS + Datos (Acceso al Almacén de Datos)

### Antes del despliegue
- [ ] Iniciativa registrada en IdAI con justificación detallada
- [ ] Revisión de IS + revisión del equipo de Datos completadas
- [ ] La evaluación de riesgo la clasifica como ELEVADA o inferior
- [ ] Revisión de clasificación de datos: ¿qué tablas del almacén de datos se acceden?
- [ ] DPIA completada si involucra datos personales
- [ ] Formación completada (incluyendo el módulo de manejo de datos)
- [ ] Acceso restringido solo al equipo onshore

### Durante la operación
- [ ] Ejecutándose en VM dedicada con acceso al almacén de datos
- [ ] Control de acceso a nivel de columna configurado
- [ ] Todas las consultas registradas con rastro de auditoría completo
- [ ] Escaneo DLP en todas las salidas
- [ ] Restricciones de exportación de datos aplicadas
- [ ] Revisión semanal de acceso por el propietario de los datos

### Cumplimiento
- [ ] Cumplimiento completo de ISO 27001
- [ ] GDPR: DPIA aprobada, consentimiento verificado
- [ ] EU AI Act: categoría de riesgo determinada
- [ ] Revisión trimestral de IS + prueba de penetración anual

---

## T4 — Producción (IS + TI Conjunto)

### Antes del despliegue
- [ ] Revisión completa del ciclo de vida por IS, TI, y el propietario de negocio
- [ ] Evaluación de riesgo: riesgo ALTO aceptado con mitigaciones
- [ ] Revisión de arquitectura de producción completada
- [ ] Plan de recuperación ante desastres documentado y probado
- [ ] Prueba de penetración de seguridad aprobada
- [ ] Clasificación de datos: todos los niveles con controles apropiados
- [ ] Desarrollo y administración solo onshore
- [ ] DPIA completa, registro del Artículo 27 de la EU AI Act

### Durante la operación
- [ ] Ejecutándose en infraestructura de producción (Hyper-V o nube)
- [ ] Monitorización 24/7 con alertas automatizadas
- [ ] Conmutación por error y redundancia configuradas
- [ ] Calendario de copias de seguridad verificado
- [ ] Plan de respuesta a incidentes activo
- [ ] Proceso de gestión de cambios para todas las actualizaciones
- [ ] Detección de anomalías en tiempo real

### Cumplimiento
- [ ] Alcance completo de certificación ISO 27001
- [ ] Cumplimiento completo de GDPR con supervisión documentada del DPO
- [ ] EU AI Act: registro de alto riesgo completo
- [ ] Revisión mensual de IS + auditoría externa trimestral
- [ ] Prueba de recuperación ante desastres ejecutada semestralmente
</content>
