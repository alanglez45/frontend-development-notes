# CSS Preprocessors

## Introducción

Un **CSS preprocessor** es un lenguaje de scripting que extiende CSS y lo compila a CSS regular. Permite escribir estilos más modulares, reutilizables y mantenibles.

### Pros y Contras

| Pros | Contras |
|---|---|
| Modularización de estilos | No es sintaxis nativa del navegador |
| Reduce redundancia con variables y mixins | Necesita compilación |
| Reutilización de código entre proyectos | |
| Nesting (anidamiento) | |

### Preprocessors vs Postprocessors

| Preprocessor | Postprocessor |
|---|---|
| Entrada: lenguaje propio (SCSS, Less, Stylus). Salida: CSS | Entrada: CSS. Salida: CSS optimizado |
| Añade variables, mixins, nesting, etc. | Aplica plugins al CSS ya escrito |
| Ej: Sass, Less, Stylus | Ej: PostCSS, Autoprefixer, CSSO |

## Preprocessors

### Sass / SCSS

Sass significa *Syntactically Awesome Stylesheets*. Tiene dos sintaxis:

- **Sass** (`.sass`): sin llaves ni punto y coma, indentación estricta
- **SCSS** (`.scss`): compatible con CSS, usa llaves y punto y coma. Es la más popular

Instalación:

```bash
npm install -g sass
# o descargar el binario desde GitHub
```

Uso:

```bash
sass input.scss output.css
sass --watch input.scss:output.css  # watch mode
```

### Less

Less es un preprocesador que corre en Node.js, en el navegador o en Rhino.

Instalación:

```bash
npm install -g less
```

Uso en consola:

```bash
lessc input.less output.css
```

Uso en navegador (solo desarrollo):

```html
<link rel="stylesheet/less" type="text/css" href="styles.less">
<script src="less.js"></script>
```

No recomendado para producción porque compilar antes de mostrar la página causa retraso.

### Stylus

Stylus es un preprocesador dinámico influenciado por Sass y Less. Sintaxis flexible (llaves y punto y coma opcionales).

Instalación:

```bash
npm install -g stylus
```

Uso:

```bash
stylus input.styl -o output.css
stylus --watch input.styl -o output.css  # watch mode
```

## Capacidades

### Variables

| Preprocessor | Sintaxis | Ejemplo |
|---|---|---|
| Sass/SCSS | `$nombre` | `$primary-color: #333;` |
| Less | `@nombre` | `@primary-color: #333;` |
| Stylus | `nombre = valor` (o `$nombre`) | `primary-color = #333` |

### Nesting (Anidamiento)

Los tres preprocesadores permiten anidar selectores igual que el HTML:

```scss
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}
```

CSS resultante:

```css
nav ul { margin: 0; padding: 0; list-style: none; }
nav li { display: inline-block; }
nav a { display: block; padding: 6px 12px; text-decoration: none; }
```

### Parent Selector (&)

El `&` referencia al selector padre:

```scss
a {
  color: #000;

  &:hover {
    color: #ccc;
  }

  > span {
    color: #ccc;
  }
}
```

### Import

Los tres preprocesadores permiten importar archivos parciales:

```scss
// _reset.scss
html, body, ul, ol {
  margin: 0;
  padding: 0;
}

// base.scss
@import 'reset';

body {
  font: 100% Helvetica, sans-serif;
  background-color: #efefef;
}
```

### Mixins

Funciones que permiten reutilizar propiedades. Aceptan parámetros.

**Sass/SCSS:**

```scss
@mixin border-radius($radius: 5px) {
  -webkit-border-radius: $radius;
  -moz-border-radius: $radius;
  -ms-border-radius: $radius;
  border-radius: $radius;
}

.box { @include border-radius(10px); }
```

**Less:**

```less
.border-radius(@radius: 5px) {
  -webkit-border-radius: @radius;
  -moz-border-radius: @radius;
  -ms-border-radius: @radius;
  border-radius: @radius;
}

.box { .border-radius(10px); }
```

**Stylus:**

```stylus
.border-radius(radius = 5px)
  -webkit-border-radius radius
  -moz-border-radius radius
  -ms-border-radius radius
  border-radius radius

.box
  border-radius(10px)
```

### Extend / Inheritance (Herencia)

Permite que un selector herede las reglas de otro.

**Sass/SCSS:**

```scss
.message {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.success {
  @extend .message;
  border-color: green;
}
```

CSS resultante:

```css
.message, .success {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.success { border-color: green; }
```

**Placeholders** (Sass): como clases pero no generan CSS hasta que se extienden:

```scss
%message {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.success { @extend %message; border-color: green; }
```

**Less:**

```less
.inline { color: red; }

.nav ul {
  &:extend(.inline);
  background: blue;
}
```

### Operadores

Los tres soportan operaciones matemáticas:

```scss
.container { width: 100%; }

article[role="main"] {
  float: left;
  width: 600px / 960px * 100%;  // 62.5%
}

aside[role="complimentary"] {
  float: right;
  width: 300px / 960px * 100%;  // 31.25%
}
```

### Loops (Sass)

```scss
@each $var in a, b, c, d {
  .#{$var} {
    background-image: url('#{$var}.png');
  }
}
```

### Funciones de color (Less)

Less incluye funciones para manipular colores:

```less
lighten(@color, 10%);
darken(@color, 10%);
saturate(@color, 10%);
desaturate(@color, 10%);
fadein(@color, 10%);
fadeout(@color, 10%);
fade(@color, 50%);
spin(@color, 10deg);
mix(@color1, @color2);
```

## CSS Postprocessors

Un **postprocessor** aplica cambios a un archivo CSS después de haber sido escrito (manualmente o generado por un preprocesador). Plugins populares:

| Postprocessor | Descripción |
|---|---|
| PostCSS | Herramienta que usa plugins JS para automatizar tareas CSS |
| Autoprefixer | Añade vendor prefixes a reglas CSS |
| CSSO | Minificador de CSS con optimizaciones estructurales |
| CSScomb | Formateador de estilo de código |
