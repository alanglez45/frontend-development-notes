# JavaScript Modules

## Introducción

Los módulos permiten descomponer el código en partes más pequeñas, reutilizables y con dependencias claras. Todo programa, en cualquier lenguaje, puede ser modularizado — incluso en assembly con subrutinas.

### Dependency graph

```
pow2 → mul → add
```

- `add` es dependencia de `mul`
- `mul` es dependencia de `pow2`
- transitivamente, `pow2` depende de `add`

### Circular dependency

Ocurre cuando dos módulos dependen mutuamente:

```
mainPage ↔ helpPage
```

Es común pero problemática: puede causar recursión no intencional y acoplamiento fuerte. Los module systems deben manejarla, pero es mejor evitarla.

## ES5 — No Modules (antes de 2015)

Hasta ES6 no existía un sistema de módulos oficial. Todo el código compartía el mismo namespace global. Los archivos debían cargarse en orden de dependencia en el HTML:

```html
<script src="add.js"></script>
<script src="mul.js"></script>
<script src="pow2.js"></script>
```

Problemas:
- Namespace global compartido — cualquier variable es accesible desde cualquier archivo
- Interfaces no claras — ¿qué entra y qué sale de cada "módulo"?
- Orden de carga propenso a errores

## Module Pattern

Solución simple usando IIFE para encapsulación:

```js
const Calculator = (function() {
  function add(a, b) { return a + b; }

  return {
    multiply(a, n) {
      let result = 0;
      for (let i = 0; i < n; i++) result = add(result, a);
      return result;
    }
  };
})();
```

Sigue siendo global, pero las interfaces están mejor definidas. El orden de carga sigue siendo necesario.

## CommonJS (Node.js)

Usado por Node.js. Proporciona módulos reales e independientes con `require` y `module.exports`.

```js
// add.js
function add(a, b) { return a + b; }
module.exports = add;

// mul.js
const add = require('./add');
function mul(a, n) {
  let result = 0;
  for (let i = 0; i < n; i++) result = add(result, a);
  return result;
}
module.exports = mul;
```

Características:
- `require()` importa (extensión `.js` opcional)
- `module.exports` exporta
- Cada módulo tiene su propio scope
- Detecta circular dependencies (al menos advierte)

## AMD (Asynchronous Module Definition)

Antes de webpack, AMD era el sistema predominante en el ecosistema JS. Implementado por Require.JS. Básicamente una capa sobre el Module pattern.

Hoy no se usa directamente, pero webpack aún lo soporta.

## ES6 Modules (ECMAScript 2015+)

Desde ES6, JavaScript tiene un sistema de módulos unificado con `import` y `export`.

```html
<script type="module" src="main.js"></script>
```

```js
// add.js
export function add(a, b) { return a + b; }

// mul.js
import { add } from './add.js';
export function mul(a, n) {
  let result = 0;
  for (let i = 0; i < n; i++) result = add(result, a);
  return result;
}

// main.js
import { mul } from './mul.js';
console.log(mul(3, 4)); // 12
```

### Named vs Default exports

```js
// Named exports (varios por módulo)
export const name = "Zorg";
export function greet() {}

// Default export (uno por módulo)
export default class Robot {}
```

```js
// Importar named
import { name, greet } from './module.js';
// Importar default
import Robot from './module.js';
// Importar todo
import * as module from './module.js';
```

### type="module"

- Los scripts con `type="module"` son **deferred** por defecto
- Usan `import` y `export`
- `globalThis` permite acceder al objeto global (rara vez necesario)
- Compatibilidad backward con `nomodule`:

```html
<script type="module" src="main.js"></script>
<script nomodule src="legacy.js"></script>
```
