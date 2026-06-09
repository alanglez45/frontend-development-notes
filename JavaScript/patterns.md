# Patterns

## Introducción

Un **design pattern** es una solución reusable a un problema común en software. Son plantillas de cómo resolver problemas, no soluciones exactas.

### Tipos de patrones

| Tipo | Descripción | Ejemplos |
|---|---|---|
| **Creational** | Inicialización y configuración de clases/objetos | Singleton, Factory, Abstract Factory, Builder |
| **Structural** | Composición de clases/objetos, desacoplar interfaz de implementación | Adapter, Facade, Decorator |
| **Behavioral** | Interacciones dinámicas entre objetos, distribución de responsabilidad | Mediator, Observer, Strategy |

## Module Pattern

Combina dos conceptos: **self-executing function** (IIFE) y **private variables/methods**. Permite emular clases con métodos y variables públicas/privadas.

```js
const Module = (function() {
  let privateVar = 0;

  function privateMethod() {
    return privateVar;
  }

  return {
    publicMethod() {
      return privateMethod();
    },
    increment() {
      privateVar++;
    }
  };
})();
```

**Pros:** datos privados, división lógica, reutilización.  
**Contras:** no se puede testear lo privado, dependencias manuales.

## Creational Patterns

### Singleton

Asegura que solo **una instancia** de una clase exista, proporcionando un punto de acceso global.

```js
const Singleton = (function() {
  let instance;

  function createInstance() {
    return { data: "I am the instance" };
  }

  return {
    getInstance() {
      if (!instance) instance = createInstance();
      return instance;
    }
  };
})();

const a = Singleton.getInstance();
const b = Singleton.getInstance();
console.log(a === b); // true
```

**Usa cuando:** necesites un punto de acceso global y asegurar una sola instancia.  
**Pros:** acceso controlado. **Contras:** problemas con testing, difícil de escalar.

### Factory

Proporciona una interfaz genérica para crear objetos, sin exponer la lógica de creación al cliente.

```js
function createVehicle(type) {
  switch (type) {
    case "car":
      return { type: "car", wheels: 4, doors: 4 };
    case "motorcycle":
      return { type: "motorcycle", wheels: 2, doors: 0 };
    default:
      throw new Error("Unknown type");
  }
}

const car = createVehicle("car");
```

**Usa cuando:** la creación de objetos es compleja o necesitas diferentes instancias según el entorno.  
**Pros:** código más flexible, interfaz única. **Contras:** difícil añadir nuevos tipos.

## Structural Patterns

### Decorator

Añade funcionalidad a un objeto existente sin alterar su estructura.

```js
class Coffee {
  cost() { return 5; }
}

function withMilk(coffee) {
  const baseCost = coffee.cost();
  coffee.cost = () => baseCost + 2;
  return coffee;
}

const myCoffee = withMilk(new Coffee());
console.log(myCoffee.cost()); // 7
```

**Usa cuando:** necesitas añadir funcionalidad sin sub-classing.

### Facade

Proporciona una interfaz de alto nivel para un cuerpo de código complejo, ocultando su complejidad.

```js
class Engine {
  start() { /* complejo */ }
  checkOil() { /* ... */ }
  fuelInject() { /* ... */ }
}

class CarFacade {
  start() {
    const engine = new Engine();
    engine.checkOil();
    engine.fuelInject();
    engine.start();
  }
}

const car = new CarFacade();
car.start(); // interfaz simple
```

**Pros:** fácil acceso a sistemas complejos, resistente a cambios.  
**Contras:** no siempre es obvio lo que pasa dentro, métodos duplicados.

## Behavioral Patterns

### Strategy

Define una familia de algoritmos, los encapsula y los hace intercambiables.

```js
const strategies = {
  add: (a, b) => a + b,
  subtract: (a, b) => a - b,
  multiply: (a, b) => a * b
};

function execute(operation, a, b) {
  return strategies[operation](a, b);
}

console.log(execute("add", 5, 3));      // 8
console.log(execute("multiply", 5, 3)); // 15
```

### Observer

Objetos se suscriben a otros objetos y son notificados cuando ocurre un evento.

```js
class Subject {
  constructor() {
    this.observers = [];
  }
  subscribe(fn) { this.observers.push(fn); }
  unsubscribe(fn) {
    this.observers = this.observers.filter(f => f !== fn);
  }
  notify(data) {
    this.observers.forEach(fn => fn(data));
  }
}

const subject = new Subject();
subject.subscribe(data => console.log("Received:", data));
subject.notify("Hello"); // "Received: Hello"
```

**Usa cuando:** tienes relaciones one-to-many. Es la base de los event handlers en el navegador.  
**Pros:** debilita el acoplamiento. **Contras:** el sistema se vuelve menos transparente.

### Mediator

Reduce dependencias caóticas entre objetos forzándolos a colaborar solo a través de un objeto mediador.

```js
class ChatRoom {
  sendMessage(user, message) {
    console.log(`${user.name}: ${message}`);
  }
}

class User {
  constructor(name, mediator) {
    this.name = name;
    this.mediator = mediator;
  }
  send(message) {
    this.mediator.sendMessage(this, message);
  }
}

const chat = new ChatRoom();
const alice = new User("Alice", chat);
const bob = new User("Bob", chat);
alice.send("Hi!");  // "Alice: Hi!"
bob.send("Hello!"); // "Bob: Hello!"
```

## Development Principles

| Sigla | Significado |
|---|---|
| **DRY** | Don't Repeat Yourself |
| **KISS** | Keep It Simple, Stupid |
| **YAGNI** | You Ain't Gonna Need It |
| **SOLID** | Single Responsibility, Open-Closed, Liskov Substitution, Interface Segregation, Dependency Inversion |
| **BDUP** | Big Design Up Front (a evitar) |
