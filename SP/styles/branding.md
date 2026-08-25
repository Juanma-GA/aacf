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

# Activos de Marca

El espejo legible por humanos de la fuente de verdad de los tokens. Los valores canónicos se redactan
como W3C Design Tokens (`design.tokens.json`, colores en OKLCH) y se compilan a propiedades
personalizadas CSS + el tema de Tailwind vía Style Dictionary — ver
[`design-system.md`](design-system.md). Nunca hardcodees estos valores en los componentes;
referencia las variables de token semánticas (HR0/HR8).

## Colores

### Paleta primaria — Azul ATEXIS
| Nombre | Hex | Uso |
|------|-----|-------|
| Primario | `#2E74B5` | Azul de marca ATEXIS — acciones primarias, estados activos, logo |
| Primario claro | `#4A8CCB` | Estados de hover, enlaces |
| Primario oscuro | `#245C90` | Estados pulsados |

### Paleta neutra
| Nombre | Hex | Uso |
|------|-----|-------|
| Fondo | `#FFFFFF` | Fondo de página |
| Superficie | `#F8FAFC` | Fondo de tarjeta/panel |
| Borde | `#E2E8F0` | Bordes, divisores |
| Texto primario | `#0F172A` | Encabezados, texto de cuerpo |
| Texto secundario | `#64748B` | Texto atenuado, etiquetas |
| Texto deshabilitado | `#94A3B8` | Elementos deshabilitados |

### Colores semánticos
| Nombre | Hex | Uso |
|------|-----|-------|
| Éxito | `#16A34A` | Confirmaciones, completado |
| Advertencia | `#D97706` | Precaución, pendiente de revisión |
| Error | `#DC2626` | Errores, acciones destructivas |
| Info | `#2563EB` | Mensajes informativos |

### Colores por nivel
| Nivel | Hex | Uso |
|------|-----|-------|
| T1 | `#22C55E` | Autoservicio, bajo riesgo |
| T2 | `#3B82F6` | Aprobado por IS, moderado |
| T3 | `#F59E0B` | IS + Datos, elevado |
| T4 | `#EF4444` | Producción, alto riesgo |

## Fuentes
| Tipo | Fuente | Peso | Tamaño |
|------|------|--------|------|
| Encabezado H1 | Inter | 700 | 2rem |
| Encabezado H2 | Inter | 600 | 1.5rem |
| Encabezado H3 | Inter | 600 | 1.25rem |
| Cuerpo | Inter | 400 | 1rem |
| Pequeño | Inter | 400 | 0.875rem |
| Mono/Código | JetBrains Mono | 400 | 0.875rem |

## Escala de espaciado
- xs: 0.25rem (4px)
- sm: 0.5rem (8px)
- md: 1rem (16px)
- lg: 1.5rem (24px)
- xl: 2rem (32px)
- 2xl: 3rem (48px)

## Radio de borde
- sm: 0.25rem
- md: 0.375rem
- lg: 0.5rem
- xl: 0.75rem
- full: 9999px

## Tokens de diseño (Propiedades personalizadas CSS)
```css
:root {
  --color-primary: 207 60% 45%;            /* ATEXIS blue #2E74B5 */
  --color-primary-foreground: 210 40% 98%;
  --color-secondary: 210 40% 96.1%;
  --color-secondary-foreground: 222.2 47.4% 11.2%;
  --color-destructive: 0 84.2% 60.2%;
  --color-accent: 210 40% 96.1%;
  --color-muted: 210 40% 96.1%;
  --color-muted-foreground: 215.4 16.3% 46.9%;
  --color-border: 214.3 31.8% 91.4%;
  --radius: 0.5rem;
}
```
</content>
