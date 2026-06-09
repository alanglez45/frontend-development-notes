# Errors & Storage

## Core JS vs Web API

JavaScript se divide en dos partes:

- **Core JS (ECMAScript)**: define los built-in objects como `Array`, `Number`, `Boolean`, `Error`, etc.
- **Web API**: el host environment (navegador) añade funcionalidades como `setTimeout`, `fetch`, `Cookies`, `Storage`, `console`, etc.

En Node.js no hay Storage API ni DOM — hay que emularlos para unit tests.

## Error Object

`Error` es un built-in object de ECMAScript, como `Array` o `Number`. Cuando ves un error en consola, es el resultado de: crear una instancia de error, lanzarla (`throw`), y no atraparla.

### Tipos de Error

| Tipo | Descripción |
|---|---|
| `RangeError` | Valor numérico fuera de rango válido |
| `ReferenceError` | Referencia inválida |
| `SyntaxError` | Error de sintaxis |
| `TypeError` | Variable o parámetro de tipo incorrecto |
| `URIError` | `encodeURI()` o `decodeURI()` con parámetros inválidos |
| `AggregateError` | Varios errores envueltos en uno (ej: `Promise.any()`) |

```js
new TypeError("Invalid type");
new RangeError("Value out of range");
// El constructor funciona con o sin new
```

## Throw

Se puede lanzar cualquier expresión como error, no solo objetos Error:

```js
throw "error message";
throw 404;
throw new Error("Something went wrong");
```

Al lanzar un error:
- La ejecución se detiene (las sentencias después de `throw` no se ejecutan)
- El control pasa al primer `catch` en el call stack
- Si no hay `catch`, el programa termina

## Try, Catch, Finally

```js
try {
  // código que puede fallar
  JSON.parse("invalid json");
} catch (error) {
  // se ejecuta solo si hay error
  console.error(error.message);
} finally {
  // se ejecuta SIEMPRE, haya error o no
  console.log("Esto corre en cualquier caso");
}
```

El `finally` es útil para limpiar recursos (cerrar conexiones, limpiar timers, etc.).

### Differential Error Handling

Como `Error` es un objeto, podemos distinguir entre tipos de error:

```js
try {
  // código riesgoso
} catch (error) {
  if (error instanceof TypeError) {
    // manejar TypeError
  } else if (error instanceof RangeError) {
    // manejar RangeError
  } else {
    // error genérico
  }
}
```

¿Se usa `try...catch` en la práctica? Depende del proyecto. El manejo de errores es complejo y cada proyecto requiere enfoques distintos. Pero tener errores en consola refleja falta de profesionalismo.

## Cookies

Las cookies son **strings** con pares `key=value` separados por punto y coma. Se transfieren en el header HTTP entre servidor y cliente.

```
mr_anderson=neo; neo=not_the_one;
```

**Cómo funcionan:**
1. El servidor responde con `set-cookie:`
2. El navegador guarda la cookie
3. La cookie se envía al servidor en **cada request** posterior (incluso en requests de imágenes)

### Atributos de Cookie

| Atributo | Descripción |
|---|---|
| `;path=path` | Ruta donde aplica la cookie |
| `;domain=domain` | Dominio |
| `;max-age=segundos` | Tiempo de vida en segundos |
| `;expires=fecha` | Fecha de expiración en formato GMT |
| `;secure` | Solo enviar sobre HTTPS |
| `;httponly` | No accesible desde JavaScript |
| `;samesite` | Controla envío en requests cross-site |

### Acceso desde JavaScript

```js
// Setear una cookie
document.cookie = "username=neo";

// Leer todas las cookies (devuelve todas en un string)
console.log(document.cookie); // "username=neo"

// Las cookies httponly NO son accesibles desde JS
```

Reglas raras:
- El valor de una cookie **no puede contener espacios en blanco**
- `document.cookie` no es una propiedad de datos, es un accessor (getter/setter)
- La asignación es tratada de forma especial — no reemplaza, sino que agrega/actualiza una cookie

## Web Storage API

El Web Storage API permite almacenar pares key/value de forma segura en el navegador.

| Nombre | Persistencia |
|---|---|
| `localStorage` | Persiste incluso al cerrar el navegador |
| `sessionStorage` | Persiste solo durante la sesión de la página (incluye recargas) |

### Métodos

| Método | Descripción |
|---|---|
| `length` | Número de elementos |
| `key(n)` | Nombre del n-ésimo key |
| `getItem(key)` | Obtener valor |
| `setItem(key, value)` | Guardar o actualizar. Lanza error si se alcanza el límite |
| `removeItem(key)` | Eliminar un key |
| `clear()` | Vaciar todo |

```js
// Guardar
localStorage.setItem("theme", "dark");

// Leer
const theme = localStorage.getItem("theme");

// Eliminar
localStorage.removeItem("theme");

// Vaciar todo
localStorage.clear();

// sessionStorage funciona igual
sessionStorage.setItem("token", "abc123");
```

### Storage Event

El evento `storage` se dispara cuando se modifica un valor desde **otra página** (otra ventana/pestaña):

```js
window.addEventListener("storage", (event) => {
  console.log(event.key, event.oldValue, event.newValue);
});
```

## Cookies vs Storage API

| Característica | Cookie | localStorage / sessionStorage |
|---|---|---|
| Capacidad | ~4 kB | ~5 MB |
| Duración | Hasta expiración o eliminación | Hasta que se elimine (sessionStorage: hasta el fin de sesión) |
| Evento | No | Sí (`storage` event) |
| Acceso | `document.cookie` (getter/setter) | Métodos: `getItem`, `setItem`, etc. |
| Envío al servidor | Se envía automáticamente | No |
| Dependencia de JS | No | Sí |

Las cookies **no están obsoletas**. Sitios complejos dependen de ellas porque se envían al servidor automáticamente. Pero para almacenar datos del lado del cliente, usa Storage API.

Pregunta típica de entrevista: **¿qué almacenar en cookies vs localStorage?** Y una respuesta incorrecta común es "username" — ¿por qué?
