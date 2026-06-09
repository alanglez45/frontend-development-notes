# AJAX & Promises

## AJAX

AJAX = **Asynchronous JavaScript And XML**. El nombre es engañoso — XMLHttpRequest funciona con cualquier tipo de dato, no solo XML. El "XML" en el nombre fue solo una excusa para poder incluir la funcionalidad en IE.

### XMLHttpRequest

```js
const xhr = new XMLHttpRequest();
xhr.addEventListener("load", function() {
  console.log(this.responseText);
});
xhr.open("GET", "https://api.spacexdata.com/v4/dragons");
xhr.send();
```

Pasos:
1. Crear instancia de `XMLHttpRequest`
2. Agregar event listeners
3. `open(method, url)` — configurar la request
4. `send()` — enviar

Propiedades importantes:
- `responseText` — la respuesta como texto
- `status` — HTTP status code (200, 404, 500, etc.)
- `responseType` — tipo de respuesta esperada

### El problema del callback hell

Con requests consecutivas (que dependen del resultado anterior), los callbacks se anidan:

```js
xhr1.addEventListener("load", function() {
  // primer request
  xhr2.addEventListener("load", function() {
    // segundo request
    xhr3.addEventListener("load", function() {
      // tercer request — callback hell
    });
    xhr3.send();
  });
  xhr2.send();
});
xhr1.send();
```

## Fetch API

ES6 introdujo `fetch()` como reemplazo moderno de XMLHttpRequest. Usa **promises** en lugar de event handlers.

```js
fetch("https://api.spacexdata.com/v4/dragons")
  .then(response => response.json())  // Response implements Body
  .then(data => console.log(data));
```

- `fetch()` retorna una promise
- `response.json()` retorna una promise
- `.then()` retorna una promise

## Promises

Una **promise** ejecuta callbacks en la cadena `.then()` de forma asíncrona cuando la promise se cumple.

Ventajas sobre XMLHttpRequest:
1. La estructura es plana (no anidada)
2. Los callbacks son llamados directamente por la promise, no a través del event loop

### Crear una Promise

```js
const promise = new Promise((resolve, reject) => {
  // async job
  const success = true;
  if (success) {
    resolve("Data");
  } else {
    reject("Error");
  }
});

promise
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

### Encadenar datos entre .then()

```js
fetch("/api/user")
  .then(response => response.json())
  .then(user => {
    return fetch(`/api/dragons/${user.favoriteDragonId}`);
  })
  .then(response => response.json())
  .then(dragon => console.log(dragon))
  .catch(error => console.error(error));
```

Cada `.then()` puede:
- Retornar un valor (se pasa al siguiente `.then()`)
- Retornar `undefined` (el siguiente recibe `undefined`)
- Retornar una nueva promise (espera a que se resuelva)

### .catch()

Atrapa errores de cualquier `.then()` anterior en la cadena.

## Brief History

- **1995**: JavaScript (Brendan Eich)
- **1996-2006**: The Dark Age — IE6 dominaba, XML estancó el desarrollo web
- **XML**: estándar no tolerante a errores, viola el Principio de Robustez (Postel's law)
- **HTML5**: construido con manejo de errores en mente, comportamiento consistente entre browsers
- **Fetch API (ES6)**: reemplaza XMLHttpRequest con promises
