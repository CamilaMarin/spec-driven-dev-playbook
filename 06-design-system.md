# 06 — Design System: De Genérico a "Sala de Lectura"

## El antes

El frontend empezó como un proyecto Next.js funcional pero visualmente genérico: grises, azules, Tailwind defaults. Funcionaba, pero no tenía identidad. Podría haber sido cualquier SaaS.

## La idea

EntreLíneas es una plataforma sobre libros, lectura, y familia. La interfaz debería sentirse como entrar a una biblioteca de barrio cálida, no a un dashboard corporativo.

La metáfora: **una sala de lectura** — maderas, papeles, latón, tinta.

## La paleta

No se eligieron colores al azar. Cada token tiene una razón:

### Maderas (estructura)
| Token | Hex | Uso |
|-------|-----|-----|
| `walnut` | #2C1F14 | Sidebar, headers, texto principal |
| `mahogany` | #4A2E1A | Hover states, depth |
| `teak` | #8B5E3C | Bordes activos, acentos cálidos |

### Metales (acción)
| Token | Hex | Uso |
|-------|-----|-----|
| `brass` | #B8892A | CTAs, focus rings, estrellas |

### Papeles (superficie)
| Token | Hex | Uso |
|-------|-----|-----|
| `parchment` | #F5EDD8 | Fondo base de la app |
| `cream` | #FBF6EC | Tarjetas, paneles |

### Tintas (texto)
| Token | Hex | Uso |
|-------|-----|-----|
| `ink` | #1E140A | Texto primario |
| `ink-soft` | #5C4A36 | Texto secundario |
| `ink-faint` | #9C8670 | Placeholders, labels |

### Estados semánticos
| Token | Hex | Significado |
|-------|-----|-------------|
| `reading` | #2E5C3E | "Leyendo" — verde biblioteca |
| `leather` | #6B3A2A | Error, acento — cuero |

## Tipografía

| Uso | Familia | Razón |
|-----|---------|-------|
| Headings, logos | Playfair Display | Editorial, serif elegante |
| UI, body, labels | Inter | Legible, moderna, neutral |

Cargadas via `next/font/google` — sin layout shift, sin flash de fuente genérica.

## Componentes con identidad

### Book Spine

```
┌──┐
│  │  ← libro vertical con título rotado
│  │     color de lomo según status
│  │     hover: elevación + sombra
└──┘
```

El componente `BookSpine` representa cada libro como un lomo en un estante:
- Color de fondo según reading status (reading=verde, read=teak, want_to_read=brass, dnf=leather)
- Título rotado 90° con CSS `writing-mode`
- Click abre popover accesible para cambiar status
- Sombra `spine` personalizada

### Book Shelf

Un estante horizontal con:
- Línea de teak como "tabla" inferior
- Drag-to-scroll en desktop
- Lomos agrupados por status

### Star Rating

Estrellas en `brass` en vez del amarillo genérico de Tailwind. Más coherente con la paleta.

## La migración

Pasar de "genérico" a "sala de lectura" fue una migración completa:

1. Definir tokens en `tailwind.config.ts`
2. Crear CSS custom properties en `globals.css`
3. Migrar TODAS las páginas y componentes (11 páginas + 15 componentes)
4. Verificar con grep: cero tokens gray/blue restantes

No fue incremental — fue un cambio de identidad completo en un milestone (M6.5).

## Utilidades globales

```css
.card-biblioteca     /* tarjeta con cream bg, borde cálido, sombra card */
.btn-primary         /* walnut bg, cream text, brass hover */
.btn-brass           /* brass outline, walnut text */
.badge-reading       /* reading green con texto oscuro */
.badge-read          /* teak bg */
.badge-pending       /* brass/parchment */
.badge-dnf           /* leather muted */
```

## Iconografía

- Lucide Icons en todo el UI (no emojis)
- Color por defecto: `teak`
- Sin emojis en chrome de la app (sí en contenido de usuario)

## La Shelf View

La biblioteca no es sólo una lista de cards. Es un **estante visual**:

```
┌─────────────────────────────────────────────┐
│  Leyendo                                     │
│  ┌──┐ ┌──┐ ┌──┐ ┌──┐                       │
│  │  │ │  │ │  │ │  │                       │
│  │  │ │  │ │  │ │  │                       │
│  └──┘ └──┘ └──┘ └──┘                       │
│  ═══════════════════════  ← teak table line │
├─────────────────────────────────────────────┤
│  Quiero leer                                 │
│  ┌──┐ ┌──┐ ┌──┐ ┌──┐ ┌──┐ ┌──┐           │
│  │  │ │  │ │  │ │  │ │  │ │  │           │
│  └──┘ └──┘ └──┘ └──┘ └──┘ └──┘           │
│  ═══════════════════════════════════         │
└─────────────────────────────────────────────┘
```

Toggle entre list view y shelf view. Preferencia guardada en localStorage.

## Por qué importa el design system en un portfolio

1. **Demuestra producto completo** — no sólo backend funcional con UI placeholder.
2. **Demuestra criterio estético** — decisiones de color, tipografía, y spacing justificadas.
3. **Demuestra coherencia** — cero tokens genéricos en el resultado final.
4. **Demuestra innovación UI** — la shelf view con book spines no es una lista de cards genérica.

Un proyecto fullstack sin identidad visual es un demo. Con identidad visual, es un producto.
