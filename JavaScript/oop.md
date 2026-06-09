# Object Oriented Programming

## Introducción

Un **objeto** es datos + comportamiento encapsulados. Los objetos se comunican entre sí mediante mensajes. Esto es análogo a las células biológicas o a computadoras en una red.

```js
// Objeto = data + behavior
const counter = {
  count: 0,           // data (state)
  increment() {       // behavior
    this.count++;
  }
};
```

## Supergreen — Classic OOP Concepts

Los 3 pilares del OOP clásico:

### Encapsulation

Proteger y desacoplar los datos del exterior, agrupando datos y comportamiento.

En JavaScript se puede implementar incluso sin clases, usando un **closure** (Reveal Module Pattern):

```js
function createCounter() {
  let count = 0; // privado

  return {
    increment() { count++; },
    getCount() { return count; }
  };
}

const c = createCounter();
c.increment();
console.log(c.getCount()); // 1
console.log(c.count);      // undefined — privado
```

La encapsulación no es exclusiva de OOP — cualquier sistema de módulos es una implementación de encapsulación.

### Inheritance

Permite extender un objeto con datos y comportamiento de otro.

```
Animal → Owl         (single inheritance)
Animal → Bird → Owl  (multi-level)
Animal, Bird → Owl   (multiple — no soportado nativamente en JS)
```

**Composición sobre herencia:** en proyectos reales, la herencia multi-nivel puede volverse un problema. Prefiere composición.

### Polymorphism

"Muchas formas" — una misma entidad puede comportarse de forma diferente según el contexto.

```js
function makeSound(animal) {
  animal.speak(); // cada animal se comporta distinto
}
```

En JavaScript, el operador `+` es polimórfico: funciona distinto con números y strings.

**Precaución:** el polimorfismo puede llevar a código impredecible si se usa con herencia.

## Divine Light — Origins of OOP

El término **Object-Oriented Programming** fue acuñado por **Alan Kay**. Su concepto original **no** era sobre herencia, sino sobre **mensajería**:

> "OOP to me means only messaging, local retention and protection and hiding of state-process, and extreme late-binding of all things."

Smalltalk (el primer lenguaje OOP) fue diseñado originalmente para **construir UIs**. En una interfaz de usuario, todo es realmente un objeto (botones, inputs, ventanas) y la comunicación mediante eventos es natural. Por eso OOP brilla en UI development.

## OOP in JavaScript

### this

`this` es el concepto más complejo. Su valor cambia según el contexto:

| Contexto | `this` apunta a |
|---|---|
| Global | `window` (o `globalThis`) |
| Function (sin strict) | `window` |
| Function (strict mode) | `undefined` |
| Constructor (con `new`) | El nuevo objeto |
| Constructor sin `new` | `window` (o `undefined` en strict) |
| Method call | El objeto propietario |
| Method asignado a variable | `window` (pierde el contexto) |
| Arrow function | El contexto padre (léxico) |

```js
const obj = {
  name: "Diva",
  greet() { console.log(this.name); }
};

obj.greet();             // "Diva"

const greetFn = obj.greet;
greetFn();               // undefined — perdió el contexto

const arrowObj = {
  name: "Arrow",
  greet: () => console.log(this.name)
};
arrowObj.greet();        // undefined — arrow no tiene su propio this
```

### bind, call, apply

```js
const obj = { name: "Zorg" };

function greet(greeting) {
  console.log(`${greeting}, ${this.name}`);
}

greet.call(obj, "Hello");   // "Hello, Zorg" — llama con this nuevo
greet.apply(obj, ["Hi"]);   // "Hi, Zorg" — args como array
const bound = greet.bind(obj);
bound("Hey");               // "Hey, Zorg" — devuelve nueva función
```

### Formas de crear objetos

```js
// Object literal — una sola instancia
const obj = { name: "Diva" };

// Constructor function
function Animal(name) {
  this.name = name;
}
const owl = new Animal("Hedwig");

// Class declaration
class Animal {
  constructor(name) {
    this.name = name;
  }
}
const owl = new Animal("Hedwig");
```

### Prototypal Inheritance

```js
// Con constructor function
function Animal(name) {
  this.name = name;
}
Animal.prototype.speak = function() {
  console.log(`${this.name} speaks`);
};

function Owl(name) {
  Animal.call(this, name);
}
Owl.prototype = Object.create(Animal.prototype);

const hedwig = new Owl("Hedwig");
hedwig.speak(); // "Hedwig speaks"

// Con class (azúcar sintáctico sobre prototipos)
class Animal {
  constructor(name) { this.name = name; }
  speak() { console.log(`${this.name} speaks`); }
}

class Owl extends Animal {
  constructor(name) { super(name); }
}
```

## Multi-pass — How to Use OOP

En Front-end development, OOP se usa principalmente como **contenedor para componentes**. Código OOP pesado (herencia multi-nivel) rara vez se ve.

Recomendaciones:
- Usa **pure functions y métodos puros** incluso dentro de objetos
- Prefiere **composición sobre herencia**
- El "Reveal Module Pattern" (closure con métodos públicos) es una alternativa simple y efectiva
- OOP brilla en UI porque los componentes son conceptualmente objetos reales
