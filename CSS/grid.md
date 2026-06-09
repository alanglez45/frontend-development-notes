# CSS Grid

CSS Grid es un sistema de maquetación **bidimensional** (filas y columnas a la vez). Es el primer módulo de CSS creado específicamente para resolver problemas de layout — antes usábamos tablas, floats, positioning e inline-block, que eran parches. Flexbox es unidimensional, Grid es bidimensional, y de hecho funcionan muy bien juntos.

## Terminología Importante

| Término | Descripción |
|---|---|
| Grid Container | El elemento con `display: grid`. Padre de todos los grid items |
| Grid Item | Los hijos directos del grid container |
| Grid Line | Las líneas divisorias que forman la estructura del grid (verticales u horizontales) |
| Grid Cell | El espacio entre dos líneas de fila y dos de columna. Es una "unidad" del grid |
| Grid Track | El espacio entre dos líneas de grid adyacentes (una columna o una fila) |
| Grid Area | El espacio rodeado por cuatro grid lines. Puede tener varias celdas |

## Resumen Rápido

### Propiedades del Padre (Grid Container)

| Propiedad | Valores |
|---|---|
| `display` | `grid` (bloque) · `inline-grid` (inline) |
| `grid-template-columns` | Lista de tamaños separados por espacio. Ej: `1fr 1fr 1fr` |
| `grid-template-rows` | Igual que columns pero para filas |
| `grid-template-areas` | Define áreas nombradas. Un `.` significa celda vacía |
| `grid-template` | Atajo: rows / columns / areas |
| `column-gap` / `row-gap` | Tamaño de los gutter (espacios entre columnas/filas) |
| `justify-items` | `stretch` (default) · `start` · `end` · `center`. Alineación en el eje de la fila |
| `align-items` | `stretch` (default) · `start` · `end` · `center` · `baseline`. Alineación en el eje de la columna |
| `justify-content` | `start` · `end` · `center` · `stretch` · `space-around` · `space-between` · `space-evenly`. Alinea el grid en el eje de la fila |
| `align-content` | Mismos valores. Alinea el grid en el eje de la columna |

### Propiedades de los Hijos (Grid Items)

| Propiedad | Valores |
|---|---|
| `grid-column-start` / `grid-column-end` | Número de línea, nombre, o `span N`. Define inicio/fin en columnas |
| `grid-row-start` / `grid-row-end` | Lo mismo pero para filas |
| `grid-column` | Atajo: `start / end` |
| `grid-row` | Atajo: `start / end` |
| `grid-area` | Nombre para usar con `grid-template-areas`, o atajo: `row-start / col-start / row-end / col-end` |
| `justify-self` | `stretch` (default) · `start` · `end` · `center`. Alinea un item en su celda (eje fila) |
| `align-self` | `stretch` (default) · `start` · `end` · `center`. Alinea un item en su celda (eje columna) |
| `place-self` | Atajo: `align-self / justify-self` |

### Unidades y Funciones Especiales

| Unidad/Función | Descripción |
|---|---|
| `fr` | Fracción del espacio restante. Ej: `1fr 1fr 1fr` = 3 columnas iguales |
| `min-content` | El tamaño mínimo del contenido |
| `max-content` | El tamaño máximo del contenido |
| `auto` | Similar a `fr` pero "pierde" contra `fr` al repartir espacio |
| `fit-content` | Usa el espacio disponible, pero nunca menos que min-content ni más que max-content |
| `minmax(min, max)` | Define un mínimo y máximo para el tamaño |
| `repeat(n, tamaño)` | Repite una definición. Ej: `repeat(3, 1fr)` |
| `auto-fill` | Llena con tantas columnas como quepan, aunque estén vacías |
| `auto-fit` | Expande las columnas existentes para llenar el espacio |

## Explicación Detallada

### display

Define el elemento como grid container. Sus hijos directos pasan a ser grid items.

```css
.container {
    display: grid;
    display: inline-grid; /* inline */
}
```

### grid-template-columns y grid-template-rows

Define las columnas y filas del grid con una lista de valores separados por espacio. Cada valor representa el tamaño de un track (columna o fila), y el espacio entre ellos representa una grid line.

Puedes nombrar las líneas con corchetes:

```css
.container {
    grid-template-columns: [first] 40px [line2] 50px [line3] auto [end];
    grid-template-rows: 25% 100px auto;
}
```

Usar `repeat()` para simplificar:

```css
.container {
    grid-template-columns: repeat(3, 1fr); /* 3 columnas iguales */
}
```

La unidad `fr` reparte el espacio sobrante en fracciones:

```css
.container {
    grid-template-columns: 1fr 1fr 1fr; /* cada columna = un tercio */
}
```

### grid-template-areas

Define una plantilla visual del grid usando nombres. Las áreas se especifican con `grid-area` en los items. Un punto (`.`) significa celda vacía.

```css
.container {
    grid-template-areas:
        "header  header  header"
        "sidebar content content"
        "footer  footer  footer";
}

.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.content { grid-area: content; }
.footer  { grid-area: footer; }
```

### grid-template

Atajo para `grid-template-rows`, `grid-template-columns` y `grid-template-areas`.

```css
.container {
    grid-template:
        "header  header  header" 100px
        "sidebar content content" 1fr
        "footer  footer  footer" 50px
        / 200px 1fr 1fr;
}
```

### column-gap y row-gap

Define el tamaño de los gutter (espacios entre columnas y filas).

```css
.container {
    column-gap: 20px;
    row-gap: 20px;
}
```

### justify-items

Alinea los grid items a lo largo del **eje de la fila** (horizontal). Aplica a todos los items del contenedor.

- `stretch` (default) — llenan todo el ancho de la celda
- `start` — al borde izquierdo de la celda
- `end` — al borde derecho
- `center` — centrados en la celda

### align-items

Alinea los grid items a lo largo del **eje de la columna** (vertical). Aplica a todos los items.

- `stretch` (default) — llenan todo el alto de la celda
- `start` — al borde superior
- `end` — al borde inferior
- `center` — centrados
- `baseline` — alineados por la línea base del texto

### justify-content

Alinea el **grid completo** a lo largo del eje de la fila (horizontal). Solo tiene efecto si el grid es más pequeño que el contenedor.

- `start` (default) — al borde izquierdo
- `end` — al borde derecho
- `center` — centrado
- `stretch` — estira los items para llenar el ancho
- `space-around` — espacio igual entre items, mitad en los extremos
- `space-between` — espacio igual entre items, sin espacio en extremos
- `space-evenly` — espacio igual entre todos, incluidos extremos

### align-content

Alinea el **grid completo** a lo largo del eje de la columna (vertical). Mismos valores que `justify-content`.

### grid (atajo general)

Atajo para `grid-template-rows`, `grid-template-columns`, `grid-template-areas`, `grid-auto-rows`, `grid-auto-columns` y `grid-auto-flow`.

```css
.container {
    grid: 100px 1fr / 200px 1fr;
    /* filas: 100px 1fr / columnas: 200px 1fr */
}
```

### grid-column-start, grid-column-end, grid-row-start, grid-row-end

Determinan la posición de un item en el grid indicando las líneas donde empieza y termina.

```css
.item {
    grid-column-start: 2;
    grid-column-end: 4;
    grid-row-start: 1;
    grid-row-end: 3;
}
```

También puedes usar `span` para abarcar cierta cantidad de tracks:

```css
.item {
    grid-column: 1 / span 2; /* empieza en línea 1, abarca 2 columnas */
    grid-row: 1 / 3;         /* empieza en línea 1, termina en línea 3 */
}
```

Si no se declara el final, el item abarca 1 track por defecto. Los items pueden solaparse; usa `z-index` para controlar el orden.

### grid-column y grid-row

Atajos para `grid-column-start / grid-column-end` y `grid-row-start / grid-row-end`.

```css
.item {
    grid-column: 1 / 4;
    grid-row: 1 / 3;
}
```

### grid-area

Sirve para dos cosas:

1. Darle un nombre al item para usarlo con `grid-template-areas`
2. Como atajo de `grid-row-start / grid-column-start / grid-row-end / grid-column-end`

```css
.item {
    grid-area: header; /* para usar con grid-template-areas */
}

.item {
    grid-area: 1 / 1 / 3 / 4; /* row-start / col-start / row-end / col-end */
}
```

### justify-self

Alinea **un solo item** dentro de su celda en el eje de la fila. Sobrescribe `justify-items` del contenedor.

- `stretch` (default) — llena el ancho de la celda
- `start` · `end` · `center`

### align-self

Alinea **un solo item** dentro de su celda en el eje de la columna. Sobrescribe `align-items`.

- `stretch` (default) — llena el alto de la celda
- `start` · `end` · `center`

### place-self

Atajo para `align-self` y `justify-self` juntos.

```css
.item {
    place-self: center center; /* align / justify */
}
```

## Flexbox vs Grid

| Grid | Flexbox |
|---|---|
| Bidimensional (filas y columnas a la vez) | Unidimensional (solo filas O solo columnas) |
| Layout-first: diseñas primero la estructura | Content-first: los elementos definen el layout |
| Ideal para layouts grandes de página | Ideal para componentes y layouts pequeños |
| Mayor control sobre la colocación de elementos | Mayor flexibilidad para alinear contenido |

Se pueden (y recomiendan) usar juntos.
