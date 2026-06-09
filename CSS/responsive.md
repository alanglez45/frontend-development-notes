# CSS Responsive Design

### Responsive vs Adaptive

- **Responsive Web Design (RWD)**: un solo sitio que se adapta del lado del cliente según las características del dispositivo (ancho de viewport, resolución). Usa media queries, grids flexibles e imágenes responsivas.
- **Adaptive Web Design (AWD)**: el servidor detecta el dispositivo y envía archivos específicos para ese dispositivo (CSS, HTML, imágenes diferentes).

En la práctica los proyectos usan ambos: RWD casi siempre, pero también partes de AWD como SSR para SEO, optimización de imágenes del lado del servidor, o versiones distintas según geolocalización.

### Mobile First

El concepto de Eric Schmidt y Luke Wroblewski: diseñar primero para móvil y luego escalar hacia arriba. Se puede ver en tres niveles:

1. **Principio de producto** — el equipo debe enfocarse en la solución móvil primero
2. **Metodología de diseño** — empezar por el móvil y crear versiones más grandes después
3. **Implementación** — el primer CSS es para móvil, las demás vistas se agregan con `min-width`

En código, mobile first significa que el CSS base (sin media queries) es para la vista más angosta, y luego se usa `min-width` para pantallas más grandes.

### Progressive Enhancement vs Graceful Degradation

| Progressive Enhancement | Graceful Degradation |
|---|---|
| Empieza con lo esencial para todos, luego agrega mejoras para navegadores modernos | Empieza con la experiencia completa, luego parchea para navegadores viejos |
| "Capa base + mejoras" | "Experiencia completa + parches" |

Ambos buscan lo mismo: que el producto sea útil para todos los usuarios. Hoy en día seguimos usando ambos enfoques — sitios web se ven "gracefully degraded" en navegadores no soportados, mientras que para los soportados la experiencia está bien definida.

### Viewport Meta Tag

Sin esta etiqueta, los smartphones encogen automáticamente la página para que quepa en la pantalla (como si vieras un sitio de escritorio en miniatura). Con ella, el dispositivo sabe que el sitio ya está optimizado para móvil y no debe hacer zoom automático.

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

- `width=device-width` — el ancho del viewport iguala al ancho del dispositivo
- `initial-scale=1` — sin zoom inicial

### Touchscreens vs Cursors

Las pantallas táctiles no soportan `:hover` como tal (no hay cursor). Requieren principios de diseño distintos. Además, conviene que el sitio sea navegable solo con teclado por accesibilidad.

### Performance en Móvil

El objetivo principal de las hojas de estilo móviles es alterar el layout y **reducir el ancho de banda**. Un desafío: las pantallas retina requieren imágenes de alta resolución, lo que puede hacer que un móvil necesite imágenes más pesadas que un escritorio. Una solución es comprimir más las imágenes para móvil (los artefactos de compresión se notan menos en pantallas pequeñas).

### Media Queries

Permiten aplicar CSS condicionalmente según características del dispositivo.

```css
/* Mobile first (min-width) */
@media (min-width: 768px) { ... }

/* Desktop first (max-width) */
@media (max-width: 768px) { ... }

/* Rango */
@media (min-width: 576px) and (max-width: 768px) { ... }
```

**Tipos de Media:**

| Tipo | Descripción |
|---|---|
| `all` | Todos los dispositivos |
| `print` | Impresión y vista previa |
| `screen` | Pantallas |
| `speech` | Síntesis de voz |

**Operadores Lógicos:**

| Operador | Descripción |
|---|---|
| `and` | Combina condiciones |
| `not` | Niega la consulta |
| `only` | Previene aplicar estilos en navegadores viejos |
| `,` (coma) | OR entre queries |

**Componentes de una media query:**
- **Media type**: `screen`, `print`, `all`, `speech`
- **Media feature**: `min-width`, `max-width`, `resolution`, etc.
- **Logical operator**: `and`, `not`, `only`, `,`

### width

La media feature que más se usa. Siempre en **CSS pixels** (no device pixels). Los CSS pixels son una unidad lógica que hace que un sitio se vea similar en dispositivos del mismo tamaño físico, sin importar la resolución de la pantalla.

- `min-width` — se activa cuando el viewport mide **al menos** ese valor (mobile first)
- `max-width` — se activa cuando el viewport mide **como máximo** ese valor (desktop first)

### Breakpoints

Puntos donde cambia el layout. Bootstrap define estos breakpoints estándar:

| Breakpoint | Mínimo | Dispositivo |
|---|---|---|
| Extra small | < 576px | Phones |
| Small | ≥ 576px | Landscape phones |
| Medium | ≥ 768px | Tablets |
| Large | ≥ 992px | Desktops |
| Extra large | ≥ 1200px | Large desktops |

```css
/* Small (landscape phones, 576px+) */
@media (min-width: 576px) { ... }

/* Medium (tablets, 768px+) */
@media (min-width: 768px) { ... }

/* Large (desktops, 992px+) */
@media (min-width: 992px) { ... }

/* Extra large (large desktops, 1200px+) */
@media (min-width: 1200px) { ... }
```

No mezcles `min-width` y `max-width` si puedes evitarlo. Usa uno de los dos enfoques consistentemente.

### Retina / HiDPI

En pantallas retina (como las de Apple), un CSS pixel equivale a 2 o más device pixels. Para que las imágenes se vean nítidas necesitas imágenes de mayor resolución. Puedes usar media queries con `min-resolution` o con el prefijo `-webkit-min-device-pixel-ratio`:

```css
@media (-webkit-min-device-pixel-ratio: 1.5),
       (min-resolution: 1.5dppx),
       (min-resolution: 144dpi) {
  .element {
    background-image: url("image_2x.jpg");
  }
}
```

### srcset (imágenes en HTML)

El atributo `srcset` le permite al navegador elegir la imagen más adecuada según el tamaño de pantalla y la resolución:

```html
<img
  sizes="(max-width: 30em) 100vw, (max-width: 50em) 50vw"
  srcset="rowing-200.jpg 200w,
          rowing-400.jpg 400w,
          rowing-800.jpg 800w"
  src="rowing-400.jpg"
  alt="...">
```

- **`srcset`**: lista de imágenes con su ancho en pixels (`200w`) o densidad de pixels (`2x`)
- **`sizes`**: le dice al navegador cuánto espacio va a ocupar la imagen en cada breakpoint
- **`src`**: fallback para navegadores que no soportan srcset
