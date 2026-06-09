# ESNext

**ESNext** es el nombre informal para las versiones modernas de ECMAScript (ES6/ES2015 en adelante). A partir de 2015, el estándar adoptó lanzamientos anuales confeatures graduales. "ESNext" se refiere tanto a lo ya publicado como a propuestas futuras (TC39 Proposals) que aún no son oficiales pero ya se pueden usar con transpiladores como Babel o TypeScript.

## Arrow Functions

Son **expresiones**, no reemplazan a las function declarations. Dos diferencias principales con funciones tradicionales:
1. Sintaxis mucho más compacta
2. No tienen su propio `this`, `arguments`, ni compatibilidad con `bind/call/apply`

Están diseñadas para usarse como **callbacks**, principalmente en programación funcional.

```js
// No existe arrow function declaration
const add = (a, b) => a + b;        // expresión
const square = x => x * x;           // sin () si 1 parámetro
const random = () => Math.random();  // () obligatorio si 0 params
const obj = () => ({ key: "value" }); // () extra para objeto literal
```

**Ventaja:** menos ruido, más enfoque en los datos.

```js
// Tradicional
[1, 2, 3].map(function(n) { return n * 2; });
// Arrow
[1, 2, 3].map(n => n * 2);
```

## Spread Syntax

Expande iterables (strings, arrays, Map, Set, array-like) en lugares donde se esperan múltiples valores.

```js
// Arrays
const arr = [1, 2, 3];
const copy = [...arr];           // shallow copy
const combined = [...arr, 4, 5]; // agregar elementos

// Strings
const chars = [..."hello"]; // ["h", "e", "l", "l", "o"]

// Objects
const obj = { a: 1, b: 2 };
const merged = { ...obj, c: 3 }; // { a: 1, b: 2, c: 3 }

// Condicional
const user = { name: "Zorg", ...(isAdmin && { role: "admin" }) };
```

## Rest Parameter

Ensambla múltiples parámetros en un array (lo opuesto a spread).

```js
function sum(...numbers) {
  return numbers.reduce((acc, n) => acc + n, 0);
}
console.log(sum(1, 2, 3, 4)); // 10

// Con otros parámetros
function greet(greeting, ...names) {
  return names.map(n => `${greeting}, ${n}`);
}
```

- Solo un rest parameter por función
- Debe ser el último parámetro
- Es un array real (a diferencia de `arguments`)

## Destructuring

Descompone arrays y objetos en variables.

### Arrays

```js
const [a, b, c] = [1, 2, 3];
const [first, ...rest] = [1, 2, 3, 4]; // first=1, rest=[2,3,4]
const [x, y] = [1]; // x=1, y=undefined

// Intercambiar variables
[a, b] = [b, a];
```

### Objects

```js
const user = { name: "Diva", age: 42, role: "admin" };

const { name, age } = user;               // misma propiedad → variable
const { name: userName, role } = user;    // nueva variable name
const { missing = "default" } = user;     // default value
const { ...rest } = user;                 // rest properties

// Paréntesis obligatorio si no hay declaración
({ name } = user);
```

### Combinado

```js
const robots = [
  { name: "Wall-E", type: "Waste" },
  { name: "EVE", type: "Scanner" }
];

const [{ name: firstBot }] = robots;
// firstBot = "Wall-E"
```

## Object Shorthand

```js
const name = "Zorg";
const age = 42;

// Shorthand properties
const user = { name, age }; // en vez de { name: name, age: age }

// Shorthand methods
const obj = {
  greet() { console.log("Hi"); } // en vez de greet: function() {}
};

// Computed property names
const key = "dynamicKey";
const obj2 = {
  [key]: "value",
  [`${key}2`]: "value2"
};
```

## ES20XX Features

### ES2017

| Feature | Descripción |
|---|---|
| `String.padStart()`, `String.padEnd()` | Rellenar string hasta cierta longitud |
| `Object.values()` | Valores de un objeto como array |
| `Object.entries()` | Pares [key, value] como array |
| `async/await` | Código asíncrono con sintaxis clara |

### ES2018

| Feature | Descripción |
|---|---|
| Object spread (`...obj`) | Spread para objetos |
| `Promise.prototype.finally()` | Código que corre siempre, resolved o rejected |
| Asynchronous iterators | `for await...of` |
| Async generators | `async function*` |

### ES2019

| Feature | Descripción |
|---|---|
| `Array.flat(depth)` | Aplana arrays anidados |
| `Array.flatMap(fn)` | map + flat en uno |
| `Object.fromEntries()` | Array de [key, value] → objeto |
| Optional catch binding | `catch { }` sin parámetro |
| `String.trimStart()`, `String.trimEnd()` | Quitar whitespace |

### ES2020

| Feature | Descripción |
|---|---|
| `??` (Nullish coalescing) | `null ?? "default"` — solo para null/undefined |
| `?.` (Optional chaining) | `user?.address?.city` |
| `Promise.allSettled()` | Espera todas las promises, fallen o no |
| Dynamic import | `import('module')` en runtime |
| `BigInt` | Números enteros más grandes que 2⁵³-1 |
| `globalThis` | Acceso global cross-environment |

```js
const value = null ?? "default"; // "default"
const street = user?.address?.street; // undefined si falta algo
import(`./lang-${locale}.js`).then(module => module.init());
```

### ES2021

| Feature | Descripción |
|---|---|
| `String.replaceAll()` | Reemplaza todas las ocurrencias |
| Logical assignment (`\|\|=`, `&&=`, `??=`) | Combinan operadores lógicos con asignación |
| Numeric separator (`1_000_000`) | Más legible |
| `Promise.any()` | Retorna la primera promise que se resuelva |

```js
"a-b-c".replaceAll("-", " "); // "a b c"
x ??= "default"; // solo si x es null/undefined
const budget = 1_000_000; // 1000000
```

### ES2022

| Feature | Descripción |
|---|---|
| Private fields (`#field`) | Campos privados en clases |
| Private methods (`#method()`) | Métodos privados en clases |
| Top-level await | `await` sin `async` en módulos |
| `Array.at(index)` | Acceder con índices negativos |

```js
class Counter {
  #count = 0;           // private field
  #log() { /* ... */ }  // private method
  increment() { this.#count++; }
}
const arr = [1, 2, 3];
arr.at(-1); // 3 (último elemento)
```
