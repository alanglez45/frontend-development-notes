# CSS Methodologies

## Introducción

El CSS no estructurado en proyectos grandes se convierte en un desastre: cambios mínimos arreglan un problema pero crean tres más, requieren hacks feos, y pequeños cambios en CSS pueden romper funcionalidad de JavaScript.

### Beneficios de una buena arquitectura CSS

- Menos reglas de estilo
- Menos colisiones de estilos
- Mantenibilidad a largo plazo
- Incorporación más rápida para nuevos miembros
- Colaboración más fácil entre el equipo
- Transiciones de proyecto más suaves

## Separation of Concerns (Separación de Preocupaciones)

La idea clásica: HTML es la estructura, CSS es la apariencia, JavaScript es el comportamiento.

```html
<!-- Bien: HTML solo describe contenido -->
<p class="greeting">Hello there!</p>

<!-- Mal: HTML contiene decisiones de estilo -->
<p style="text-align: center;">Hello there!</p>
```

Pero en la práctica, el CSS suele ser un espejo del markup — los selectores reflejan la estructura del HTML. El objetivo es **desacoplar** los estilos de la estructura del DOM, manteniendo la especificidad baja.

### Content-agnostic CSS + Utility Classes

Cuando dos componentes no tienen nada en común semánticamente pero sí en diseño (ej: `.company-info` y `.article-preview` ambos usan bordes redondeados y sombra), se crea un componente abstracto basado en lo que comparten.

Esto se expande a utility classes: clases pequeñas y reutilizables, cada una con un conjunto mínimo de reglas CSS. Un elemento HTML puede tener muchas clases.

```html
<div class="bg-white rounded shadow p-4">...</div>
```

Esto prefiere **composición sobre duplicación**, pero produce HTML que se parece a inline styles. Aquí el HTML está muy preocupado por las clases CSS existentes (lo opuesto a separation of concerns).

## BEM (Block, Element, Modifier)

Es un enfoque **basado en componentes**. Divide la interfaz en bloques independientes, reutilizables y escalables.

### Regla principal: solo usar selectores de clase

| No usar | Razón |
|---|---|
| IDs | No se pueden reutilizar |
| Selectores de etiqueta | El markup es inestable (cambia de `<h1>` a `<h3>`, de `<p>` a `<div>`) |
| CSS Reset | Afecta todos los nodos, viola la independencia de componentes |
| Selector universal `*` | Hace el código impredecible, limita la reutilización |
| Selectores anidados | Aumentan el acoplamiento y dificultan la reutilización |

### Block

Componente funcionalmente independiente y reutilizable. El nombre describe su propósito ("¿Qué es?") no su estado ("¿Cómo se ve?").

```html
<div class="menu">...</div>
```

Los bloques pueden:
- Anidarse unos dentro de otros
- Moverse libremente entre páginas y proyectos
- Tener múltiples instancias en una interfaz

### Element

Parte compuesta de un bloque que no se puede usar separada de él.

```html
<div class="menu">
  <span class="menu__item">...</span>
</div>
```

Reglas:
- Nombre: `block-name__element-name` (doble underscore `__`)
- Pueden anidarse, pero un elemento siempre es parte de un **bloque**, no de otro elemento (no `block__elem1__elem2`)
- No es obligatorio: no todos los bloques tienen elementos

### Modifier

Define la apariencia, estado o comportamiento de un bloque o elemento.

**Boolean** (solo importa si está presente):

```html
<button class="button button_disabled">Submit</button>
```

**Key-value** (el valor importa):

```html
<button class="button button_size_l">Large</button>
<button class="button button_theme_islands">Islands</button>
```

Un modifier nunca se usa solo — siempre modifica a un bloque o elemento.

### Mix

Técnica para usar diferentes entidades BEM en un solo nodo DOM:

```html
<div class="menu header__menu">...</div>
```

Así el bloque `menu` se convierte también en elemento `header__menu`, combinando comportamiento y estilos sin duplicar código.

## OOCSS (Object-Oriented CSS)

Dos ideas principales:
1. **Separación de estructura y diseño** — un objeto tiene estructura base (width, height, padding) y el diseño se aplica por separado
2. **Separación de contenedor y contenido** — los estilos no deben depender del contenedor donde están

**Bueno**: reduce la cantidad de código reutilizando (DRY).  
**Malo**: soporte complejo — cambiar el estilo de un elemento puede requerir cambiar CSS y también clases en el markup.

## SMACSS (Scalable and Modular Architecture for CSS)

Divide los estilos en 5 partes:

| Categoría | Descripción |
|---|---|
| **Base** | Estilos de elementos principales: `body`, `input`, `button`, `ul`, etc. Usa principalmente etiquetas HTML |
| **Layout** | Estilos de elementos globales: header, footer, sidebar. Sugiere IDs (aunque es mala práctica) |
| **Module** | Bloques reutilizables. No usar ID ni selectores de etiqueta |
| **State** | Estados de módulos y base. Única sección donde es aceptable `!important` |
| **Theme** | Estilos de diseño que podrías necesitar reemplazar |

## Atomic CSS

Cada propiedad reutilizable tiene su propia clase:

```css
.mt-1 { margin-top: 1px; }
.w-200 { width: 200px; }
.bg-blue { background-color: blue; }
```

```html
<div class="mt-1 w-200 bg-blue">...</div>
```

**Bueno**: minimiza la cantidad de CSS, fácil de modificar.  
**Malo**: los nombres de clase describen propiedades no semántica, las configuraciones de visualización están directamente en el HTML.

## MCSS (Multilayer CSS)

Divide los estilos en capas:

| Capa | Descripción |
|---|---|
| **Zero layer (foundation)** | Reset de estilos del navegador (reset.css o normalize.css) |
| **Base layer** | Estilos de elementos reutilizables: botones, inputs, tooltips |
| **Project layer** | Módulos separados y "contexto": modificaciones según navegador, dispositivo, rol de usuario |
| **Cosmetic layer** | Estilo OOCSS/atómico para cambios menores de apariencia |

La jerarquía es importante:
- Base no afecta a otras capas
- Elementos de Base solo afectan a su propia capa
- Project layer puede afectar a Base y Project
- Cosmetic layer se aplica selectivamente en el markup

## FUN (Flat Hierarchy, Utility, Name-spaced)

Principio detrás de cada letra:

| Letra | Significado |
|---|---|
| **F**lat hierarchy | Usar clases para seleccionar elementos, evitar cascada innecesaria, no usar IDs |
| **U**tility styles | Crear clases atómicas de servicio: `w100` para `width: 100%`, `fr` para `float: right` |
| **N**ame-spaced components | Añadir namespaces a los selectores para evitar colisiones de nombres de clase |

Es un enfoque ligero: impone pocos requisitos, solo establece la forma preferida de escribir selectores y usarlos en el markup. En proyectos pequeños puede ser suficiente.

## Comparativa Rápida

| Metodología | Enfoque | Ideal para |
|---|---|---|
| BEM | Bloques, elementos, modificadores | Proyectos grandes con equipos |
| OOCSS | Objetos reutilizables, separar estructura de diseño | Proyectos modulares |
| SMACSS | 5 categorías de estilos | Proyectos que necesitan categorización clara |
| Atomic CSS | Una clase por propiedad | Equipos que priorizan velocidad sobre semántica |
| MCSS | Capas con jerarquía | Proyectos grandes y complejos |
| FUN | Namespaces + utilities + jerarquía plana | Proyectos pequeños y medianos |
