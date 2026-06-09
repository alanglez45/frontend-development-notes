# CSS Flexbox

Flexbox es un modelo de maquetación que distribuye elementos en una sola dirección (fila o columna). Resuelve problemas como centrar verticalmente, repartir el espacio equitativamente o hacer que columnas tengan la misma altura — cosas que antes eran complicadas con floats y positioning.

## El Modelo Flex

| Término | Descripción |
|---|---|
| Eje principal (main axis) | La dirección en la que se colocan los elementos |
| Eje transversal (cross axis) | La dirección perpendicular al eje principal |
| Contenedor flex (flex container) | El elemento padre que tiene `display: flex` |
| Elementos flex (flex items) | Los hijos directos del contenedor |

## Resumen Rápido

### Propiedades del Padre (Contenedor)

| Propiedad | Valores |
|---|---|
| `flex-direction` | `row` (default) · `row-reverse` · `column` · `column-reverse` |
| `flex-wrap` | `nowrap` (default) · `wrap` · `wrap-reverse` |
| `flex-flow` | Atajo: `flex-direction` + `flex-wrap` (default: `row nowrap`) |
| `justify-content` | `flex-start` (default) · `flex-end` · `center` · `space-between` · `space-around` · `space-evenly` |
| `align-items` | `stretch` (default) · `flex-start` · `flex-end` · `center` · `baseline` |
| `align-content` | `stretch` (default) · `flex-start` · `flex-end` · `center` · `space-between` · `space-around` |
| `gap` / `row-gap` / `column-gap` | Espacio entre elementos (no en los bordes). También funciona en Grid |

### Propiedades de los Hijos (Elementos)

| Propiedad | Valores |
|---|---|
| `order` | `0` (default). Controla el orden visual |
| `flex-grow` | `0` (default). Proporción para crecer. Si todos son `1`, el espacio se reparte igual. `2` = el doble |
| `flex-shrink` | `1` (default). Capacidad de encogerse. `0` = no se encoge |
| `flex-basis` | `auto` (default) · `0` · medida (`200px`, `20%`). Tamaño inicial antes de distribuir espacio |
| `flex` | Atajo: `flex-grow` `flex-shrink` `flex-basis`. Default: `0 1 auto`. Un solo valor como `flex: 5` cambia basis a `0%` |
| `align-self` | `auto` (default) · `flex-start` · `flex-end` · `center` · `stretch` · `baseline`. Sobrescribe `align-items` en un elemento |

## Explicación Detallada

### flex-direction

Define la dirección del eje principal, es decir, si los elementos se colocan en fila (horizontal) o en columna (vertical). También controla si empiezan desde el inicio o al revés.

```css
.container {
    flex-direction: row;            /* default: izquierda a derecha */
    flex-direction: row-reverse;    /* derecha a izquierda */
    flex-direction: column;         /* arriba a abajo */
    flex-direction: column-reverse; /* abajo a arriba */
}
```

### flex-wrap

Por defecto todos los elementos intentan entrar en una sola línea. Con esta propiedad puedes permitir que se envuelvan a varias líneas si no caben.

```css
.container {
    flex-wrap: nowrap;      /* default: todos en una línea */
    flex-wrap: wrap;        /* envuelve a varias líneas */
    flex-wrap: wrap-reverse; /* envuelve en dirección opuesta */
}
```

### flex-flow

Atajo para escribir `flex-direction` y `flex-wrap` juntos. Valor por defecto: `row nowrap`.

```css
.container {
    flex-flow: row wrap;
}
```

### justify-content

Controla cómo se alinean los elementos a lo largo del **eje principal**. Se usa para distribuir el espacio sobrante cuando los elementos no ocupan todo el ancho/alto.

```css
.container {
    justify-content: flex-start;      /* default: todos al inicio */
    justify-content: flex-end;        /* todos al final */
    justify-content: center;          /* centrados */
    justify-content: space-between;   /* primero al inicio, último al final, espacio igual entre ellos */
    justify-content: space-around;    /* espacio igual alrededor de cada elemento */
    justify-content: space-evenly;    /* espacio igual entre todos, incluidos extremos */
}
```

### align-items

Controla cómo se alinean los elementos a lo largo del **eje transversal** (el perpendicular al principal). Es como `justify-content` pero para el otro eje.

```css
.container {
    align-items: stretch;      /* default: los elementos se estiran */
    align-items: flex-start;   /* alineados al inicio del eje transversal */
    align-items: flex-end;     /* alineados al final */
    align-items: center;       /* centrados */
    align-items: baseline;     /* alineados por la línea base del texto */
}
```

### align-content

Alinea las líneas del contenedor cuando hay espacio extra en el eje transversal y los elementos están envueltos en varias líneas (necesita `flex-wrap: wrap`). Es como `justify-content` pero para líneas completas en lugar de elementos individuales.

```css
.container {
    align-content: stretch;        /* default: líneas se estiran */
    align-content: flex-start;     /* líneas al inicio */
    align-content: flex-end;       /* líneas al final */
    align-content: center;         /* líneas centradas */
    align-content: space-between;  /* primera al inicio, última al final */
    align-content: space-around;   /* espacio igual alrededor de cada línea */
}
```

### gap, row-gap, column-gap

Controla el espacio entre los elementos flexibles. El espacio solo se aplica entre elementos, no en los bordes exteriores. Si usas `justify-content: space-between`, el gap solo tiene efecto si el espacio sobrante es menor. También funciona en Grid y multi-column.

```css
.container {
    gap: 10px;
    row-gap: 10px;
    column-gap: 20px;
}
```

### order

Por defecto los elementos se muestran en el orden en que están en el HTML. Con `order` puedes cambiar el orden visual sin modificar el HTML. Los elementos con el mismo valor de `order` mantienen el orden original.

```css
.item:nth-child(1) { order: 3; }
.item:nth-child(2) { order: 1; }
.item:nth-child(3) { order: 2; }
```

### flex-grow

Define si un elemento puede **crecer** para ocupar espacio sobrante. Acepta un número sin unidad que funciona como proporción.

```css
.item {
    flex-grow: 1; /* crece para ocupar espacio sobrante */
    flex-grow: 2; /* ocupa el doble que los demás */
}
```

Si todos tienen `flex-grow: 1`, el espacio sobrante se reparte equitativamente. Si uno tiene `flex-grow: 2`, ese elemento ocupa el doble de espacio que los demás. Los números negativos no son válidos.

### flex-shrink

Define si un elemento puede **encogerse** cuando no hay suficiente espacio.

```css
.item {
    flex-shrink: 1; /* default: puede encogerse */
    flex-shrink: 0; /* no se encoge aunque desborde */
}
```

Los números negativos no son válidos.

### flex-basis

Define el **tamaño inicial** de un elemento **antes** de repartir el espacio sobrante. Puede ser una medida fija (`200px`, `20%`) o una palabra clave.

```css
.item {
    flex-basis: auto;     /* default: usa width/height */
    flex-basis: 200px;    /* tamaño fijo */
    flex-basis: 0;        /* no cuenta el contenido para el reparto */
    flex-basis: content;  /* según el contenido (poco soportado) */
}
```

### flex

Atajo para escribir `flex-grow`, `flex-shrink` y `flex-basis` juntos. Los dos últimos son opcionales.

```css
.item {
    flex: 0 1 auto; /* default */
    flex: 1;        /* flex-grow: 1; flex-shrink: 1; flex-basis: 0% */
    flex: 2 1 20%;
}
```

Si usas un solo número como `flex: 5`, el `flex-basis` pasa a ser `0%`. Se recomienda usar este atajo en lugar de las propiedades individuales.

### align-self

Permite cambiar la alineación de **un solo elemento** sin afectar a los demás. Sobrescribe lo que se haya puesto en `align-items` del contenedor.

```css
.item {
    align-self: flex-start; /* al inicio del eje transversal */
    align-self: flex-end;   /* al final */
    align-self: center;     /* centrado */
    align-self: stretch;    /* estirado */
}
```

> **Nota:** `float`, `clear` y `vertical-align` no tienen efecto en elementos flex.
