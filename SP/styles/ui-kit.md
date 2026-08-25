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

# Kit de UI — Definiciones de la Biblioteca de Componentes

## Sistema de diseño

### Botones
| Variante | Uso | Clases |
|---------|-------|---------|
| Primario | Acciones principales | `bg-primary text-primary-foreground hover:bg-primary/90` |
| Secundario | Acciones secundarias | `bg-secondary text-secondary-foreground hover:bg-secondary/80` |
| Destructivo | Eliminar/quitar | `bg-destructive text-destructive-foreground hover:bg-destructive/90` |
| Contorno | Acciones terciarias | `border border-input bg-background hover:bg-accent` |
| Fantasma (Ghost) | Acciones en línea | `hover:bg-accent hover:text-accent-foreground` |
| Icono | Botones solo de icono | `h-10 w-10 rounded-full` |

### Formularios
- Usa los componentes Form de `shadcn/ui` con react-hook-form + validación zod
- La etiqueta siempre encima del campo
- Mensajes de error debajo del campo en color destructivo
- Campos obligatorios marcados con asterisco
- Botones de envío deshabilitados durante la carga (mostrar spinner)

### Tablas
- Usa el DataTable de `shadcn/ui` con TanStack Table
- Columnas ordenables al hacer clic en el encabezado
- Paginación: 10/25/50 elementos por página
- Barra de búsqueda/filtro encima de la tabla
- Acciones de fila vía menú desplegable (columna final)
- Filas esqueleto de carga mientras se obtienen los datos

### Navegación
- Barra lateral para la navegación principal (colapsable)
- Migas de pan (breadcrumbs) para páginas profundas
- Pestañas para subsecciones dentro de una página
- Paleta de comandos (Ctrl+K) para navegación rápida

### Tarjetas
- Tarjeta estándar: borde, rounded-lg, p-6, shadow-sm
- Hover: translate-y-[-2px] shadow-md transition
- Indicador de estado: borde izquierdo coloreado
- Clic: navega a la vista de detalle

### Modales/Diálogos
- Usa el componente Dialog de `shadcn/ui`
- Centrado, max-width-lg
- Botón de cierre arriba a la derecha
- Clic en el fondo para descartar (a menos que el formulario tenga cambios)
- Trampa de foco para accesibilidad

### Toast/Notificaciones
- Usa `sonner` para las notificaciones toast
- Posición: abajo a la derecha
- Autodescarte: 5 segundos
- Tipos: éxito (verde), error (rojo), advertencia (ámbar), info (azul)
</content>
