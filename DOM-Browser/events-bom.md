# DOM Events & BOM

## Intro

### Eventos en aplicaciones con UI

Las aplicaciones con interfaz de usuario siguen este flujo:

```
start → build UI → listen to events → [event] → handle event → end
```

La aplicación no es más que configurar componentes UI y establecer event handlers.

### El problema de los event handlers

El código que asignamos a un event handler se ejecuta **después**, no sabemos exactamente cuándo. Para cuando se ejecute, el estado (DOM, datos, usuario) podría ser completamente diferente.

**Ejemplo clásico:** un formulario con validación en `onblur` de un input y submit button. Si la validación muestra un mensaje que empuja el botón hacia abajo, el click no llega al botón y el `onsubmit` nunca se dispara.

## Events

Un **event** es una señal de que algo ha ocurrido y la aplicación necesita reaccionar.

Los nombres de evento empiezan con `on` (ej: `onclick`, `onsubmit`, `onblur`).

### Event Handlers

Un event handler es una función asignada a una propiedad del elemento:

```js
element.onclick = function(event) {
  console.log("Clicked!", event);
};
```

**Con JavaScript clásico** no se usa esto porque HTML y JS deben estar separados.  
**Con React** se usa todo el tiempo.

### Event Object

Los eventos reciben un objeto como parámetro con información útil:

```js
element.onclick = function(event) {
  console.log(event.type);    // "click"
  console.log(event.target);  // elemento que disparó el evento
};
```

### addEventListener / removeEventListener

```js
function handler(event) {
  console.log("Event fired");
}

element.addEventListener("click", handler);
element.removeEventListener("click", handler);
```

`addEventListener` permite registrar **múltiples handlers** para el mismo evento (algo que `onclick` no puede hacer).

### Custom Events

```js
const event = new Event("build");
element.addEventListener("build", (e) => console.log("Built!"));
element.dispatchEvent(event);

// Con datos personalizados
const custom = new CustomEvent("login", { detail: { user: "neo" } });
element.dispatchEvent(custom);
```

### preventDefault

Previene el comportamiento por defecto del navegador:

```js
form.addEventListener("submit", (event) => {
  event.preventDefault();
  // ahora podemos manejar el submit sin recargar
});
```

### Tipos de eventos comunes

| Categoría | Eventos |
|---|---|
| Mouse | `click`, `dblclick`, `mousedown`, `mouseup`, `mousemove`, `mouseover`, `mouseout` |
| Touch | `touchstart`, `touchend`, `touchmove`, `touchcancel` |
| Keyboard | `keydown`, `keypress`, `keyup` |
| Form | `submit`, `focus`, `blur`, `change`, `input` |
| Window | `load`, `resize`, `scroll`, `hashchange`, `popstate` |
| Storage | `storage` |

## Event Phases

Hay 3 fases definidas en el estándar:

```
Window → parent → target → parent → Window
  (capture)     (target)     (bubble)
```

| Fase | Descripción |
|---|---|
| **Capture** | El evento viaja desde `Window` hasta el padre del target |
| **Target** | El evento llega al elemento target |
| **Bubble** | El evento vuelve desde el padre del target hasta `Window` |

### Bubbling (por defecto)

Los eventos "burbujean" hacia arriba: primero el hijo, luego el padre, hasta la raíz.

```html
<div id="parent">
  <button id="child">Click</button>
</div>
```

```js
document.getElementById("parent").addEventListener("click", () => {
  console.log("parent click");
});
document.getElementById("child").addEventListener("click", () => {
  console.log("child click");
});
// Click en button → "child click" → "parent click"
```

### Capturing

Con `useCapture: true`, el orden se invierte: padre primero, luego hijo.

```js
element.addEventListener("click", handler, true); // capturing
element.addEventListener("click", handler);        // bubbling (default)
```

## BOM (Browser Object Model)

El BOM representa objetos adicionales del navegador para trabajar con **todo excepto el documento**. No hay un estándar oficial, pero los navegadores modernos implementan (casi) los mismos métodos y propiedades.

### Window Object

El objeto `window` es soportado por todos los navegadores. Representa la ventana del navegador.

- Las variables globales son propiedades de `window`
- Las funciones globales son métodos de `window`
- El `document` es una propiedad de `window`

```js
window.innerWidth;   // ancho del viewport
window.innerHeight;  // alto del viewport
window.scrollTo(0, 0); // scroll arriba
```

### Navigator

```js
navigator.userAgent;      // info del browser
navigator.platform;       // "Win32", "MacIntel", etc.
navigator.cookieEnabled;  // true/false
navigator.geolocation;    // objeto para geolocalización
navigator.language;       // "es-MX", "en-US", etc.
```

### Location

Permite leer la URL actual y navegar.

```js
location.href;      // URL completa
location.protocol;  // "https:"
location.host;      // "www.amazon.com:443"
location.hostname;  // "www.amazon.com"
location.port;      // "443"
location.pathname;  // "/s"
location.search;    // "?k=edge+of+tomorrow"
location.hash;      // "#search"

// Navegar
location.href = "https://example.com";   // guarda en history
location.replace("https://example.com"); // no guarda en history
```

### History

```js
history.back();     // ←
history.forward();  // →
history.go(-2);     // retrocede 2 páginas
```

En SPAs, la gestión de history es crítica porque el navegador solo ve una página. El botón "back" puede causar problemas — hay que probar exhaustivamente.

## Event Loop

JavaScript tiene un modelo de concurrencia basado en un **event loop**, que ejecuta código, recolecta y procesa eventos, y ejecuta sub-tareas en cola.

```
Task sources (DOM events, user interaction, networking)
       ↓
   Task queue → [call stack empty?] → Call stack
       ↑                              ↓
   setTimeout                      execution
   callback
```

El `setTimeout` callback se coloca en la task queue después del delay, pero espera a que el call stack esté vacío.

### setTimeout

```js
const timerId = setTimeout(() => {
  console.log("Esto corre después");
}, 500);

clearTimeout(timerId); // cancelar
```

**No espera — es async.** El código después de `setTimeout` se ejecuta inmediatamente. El callback se ejecuta "después de al menos X ms", no exactamente en X ms.

```js
setTimeout(() => console.log("3"), 0);  // se encola
console.log("1");
console.log("2"); // 1, 2, 3 — el callback corre después
```

**Regla de oro:** nunca uses `setTimeout` para esperar algo asíncrono. Usa un callback o un evento.

### setInterval

```js
const intervalId = setInterval(() => {
  console.log("cada 100ms");
}, 100);

clearInterval(intervalId);
```

Problema: si la tarea tarda más que el intervalo, el loop consume toda la CPU.

**Mejor usar recursive setTimeout:**

```js
function tick() {
  console.log("cada 100ms entre fin e inicio");
  setTimeout(tick, 100);
}

setTimeout(tick, 100);
```
