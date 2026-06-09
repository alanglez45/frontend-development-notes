# JavaScript Functions

## Functions as First-Class Citizens

En JavaScript, las funciones son **Function Objects** — son objetos con propiedades y métodos. Esto significa que pueden:

- Guardarse en variables o arrays
- Pasarse como argumentos a otras funciones
- Retornarse desde otras funciones

A esto se le llama **"first-class citizenship"**: poder hacer lo que cualquier otro valor puede hacer.

## Function Declaration vs Function Expression

| | Function Declaration (FD) | Function Expression (FE) |
|---|---|---|
| Sintaxis | `function foo() {}` | `const foo = function() {}` |
| Hoisting | Sí (declaración completa) | Solo la variable, no la inicialización |
| Nombre | Puede ser named o anonymous | Puede ser named o anonymous |

```js
// Function Declaration
function greet(name) {
  return `Hello, ${name}`;
}

// Function Expression (anonymous)
const greet = function(name) {
  return `Hello, ${name}`;
};

// Function Expression (named — el nombre solo es accesible dentro)
const greet = function sayHello(name) {
  return `Hello, ${name}`;
};
```

## Hoisting

Hoisting significa que las declaraciones (de funciones y variables) se "elevan" al inicio del scope antes de ejecutar el código.

```js
// Funciona — la declaración de función se hoistea completa
sayHi(); // "Hi!"
function sayHi() {
  console.log("Hi!");
}

// No funciona — solo la variable se hoistea, no la asignación
sayBye(); // TypeError: sayBye is not a function
var sayBye = function() {
  console.log("Bye!");
};
```

- Con `var`, solo la **declaración** se hoistea, no la **inicialización**
- Con `let` y `const` también hay hoisting, pero no se puede acceder antes de la declaración (Temporal Dead Zone)

## Scope

El scope es la región del programa donde una variable puede ser accedida. Determina dónde el intérprete puede "ver" una variable.

```js
let global = "accesible desde cualquier lado";

function outer() {
  let outerVar = "accesible dentro de outer y sus hijas";

  function inner() {
    let innerVar = "solo dentro de inner";
    console.log(global);    // ✅
    console.log(outerVar);  // ✅
  }

  console.log(innerVar); // ❌ ReferenceError
}
```

## Closure

Un **closure** ocurre cuando una función interna tiene acceso a variables de su entorno léxico externo, incluso después de que ese entorno haya terminado.

```js
function createCounter() {
  let count = 0;
  return function() {
    count++;
    return count;
  };
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```

La función interna "recuerda" la variable `count` aunque `createCounter` ya haya terminado.

## Tipos de Funciones

### Function Declaration

```js
function named() {}
function() {} // anonymous — solo válida como expresión
```

### Function Expression

```js
const foo = function() {};        // anonymous FE
const bar = function baz() {};    // named FE — "baz" solo accesible dentro
```

### IIFE (Immediately Invoked Function Expression)

```js
(function() {
  console.log("ejecutada inmediatamente");
})();

// Con arrow function
(() => console.log("IIFE"))();
```

### Arrow Function

```js
const add = (a, b) => a + b;
const square = x => x * x;          // sin () si hay 1 parámetro
const empty = () => console.log();  // () obligatorio si 0 parámetros
```

**Diferencias clave:**
- No tienen su propio `this` — heredan del contexto padre
- No tienen acceso a `arguments`
- No son compatibles con `bind`, `call`, `apply`

### Constructor Function

```js
const func = new Function('a', 'b', 'return a + b');
```

Crea funciones que ejecutan en el scope global. Tiene problemas de seguridad y rendimiento. No se usa en el 99.99% de los casos.

## Callbacks

Un **callback** es una función que se pasa como argumento a otra función para ejecutarse después.

```js
function processUserInput(callback) {
  const name = prompt("Enter your name:");
  callback(name);
}

processUserInput(function(name) {
  console.log(`Hello, ${name}`);
});
```

La función que recibe el callback se llama **higher-order function**.

## Functional Programming (FP)

FP es un paradigma donde los programas se construyen aplicando y componiendo funciones. Se contrapone a OOP (programas construidos creando y heredando objetos).

### Conceptos clave

| Concepto | Descripción |
|---|---|
| Pure functions | Misma entrada → misma salida, sin efectos secundarios |
| Higher-order functions | Toman o retornan funciones |
| Function composition | Combinar funciones pequeñas para crear lógica compleja |
| Evitar shared state | No compartir estado entre funciones |
| Evitar mutating state | No mutar datos, crear nuevos |
| Evitar side effects | No modificar nada fuera de la función |

### Imperativo vs Declarativo

```js
// Imperativo — CÓMO hacerlo
const numbers = [1, 2, 3, 4, 5];
const doubled = [];
for (let i = 0; i < numbers.length; i++) {
  doubled.push(numbers[i] * 2);
}

// Declarativo — QUÉ quiero
const doubled = numbers.map(n => n * 2);
```

FP es declarativo.

## Pure Functions

Una función pura:
1. Dada la misma entrada, **siempre retorna la misma salida**
2. No produce **side effects**

```js
// Pura
const double = x => x * 2;

// Impura — modifica una variable externa
let tax = 0.16;
function addTax(price) {
  return price * (1 + tax);
}

// Impura — side effect (console.log)
function greet(name) {
  console.log(`Hello, ${name}`);
}
```

### Side Effects

Cualquier cambio de estado observable fuera de la función que no sea su valor de retorno:

- Modificar una variable externa o propiedad de objeto
- Hacer `console.log`
- Escribir al DOM
- Escribir a un archivo o red
- Llamar a otras funciones con side effects

### Beneficios de Pure Functions

Son más fáciles de: **leer, componer, reutilizar, testear, mover, extraer, refactorizar, reorganizar**.

## Function Composition

Composición de funciones es el proceso de combinar dos o más funciones para producir una nueva.

```js
const f = x => x + 1;
const g = x => x * 2;

const composed = x => f(g(x)); // primero g, luego f
composed(5); // g(5) = 10, f(10) = 11
```

En OOP se usa herencia; en FP se usa composición. La composición permite **single responsibility** y mejora la reutilización — creamos funciones pequeñas y las encadenamos para lógica compleja.

## Higher-Order Functions

Una **higher-order function** es cualquier función que:
- Toma una función como argumento, o
- Retorna una función, o
- Ambas

```js
// Toma una función como argumento
[1, 2, 3].map(x => x * 2);

// Retorna una función
function multiplyBy(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = multiplyBy(2);
console.log(double(5)); // 10
```

Se usan para: abstraer acciones, crear utilities reutilizables, parcial application, currying, composición.

## Recursion

Una función **recursiva** es una función que se llama a sí misma.

```js
function factorial(n) {
  if (n === 0) return 1;       // escape condition
  return n * factorial(n - 1); // llamada recursiva
}

console.log(factorial(4)); // 4*3*2*1 = 24
```

### Reglas de Recursión
1. Debe tener una **escape condition** (condición de salida) para evitar infinite loop
2. Sin escape condition, el call stack se desborda → **Maximum call stack size exceeded**

### Call Stack

JavaScript es single-threaded. Cada vez que el parser encuentra una función, la pone en el **call stack**. La recursión puede desbordar el call stack fácilmente si no hay escape condition.

### Fibonacci sin memoization

```js
function fib(n) {
  if (n <= 1) return n;
  return fib(n - 1) + fib(n - 2);
}
```

Problema: recalcula los mismos valores múltiples veces. `fib(150)` desbordaría el call stack.

## Memoization

Técnica de optimización que almacena resultados de cálculos en **caché** para reutilizarlos en lugar de recalcularlos. Se basa en dos conceptos: **closures** y **higher-order functions**.

```js
function memoizedFib() {
  const memo = {};

  function fib(n) {
    if (n <= 1) return n;

    if (memo[n] !== undefined) {
      return memo[n]; // rescata del caché
    }

    memo[n] = fib(n - 1) + fib(n - 2); // calcula y guarda
    return memo[n];
  }

  return fib;
}

const fib = memoizedFib();
console.log(fib(150)); // funciona sin desbordar
```

Ahora cada resultado calculado se guarda en el objeto `memo`, evitando recalcular los mismos valores.
