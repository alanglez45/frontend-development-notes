# DOM (Document Object Model)

El DOM es una representación del documento HTML como un árbol de nodos y objetos. Permite que programas (JavaScript) cambien la estructura, estilo y contenido de la página.

## Historia: ¿Por qué manipular el DOM?

### Static HTML pages

Tradicionalmente los servidores servían páginas HTML estáticas. Cada interacción (click, form submit) resultaba en un documento HTML completamente nuevo.

```
1ra request (GET) → sirve página HTML completa
2da request (POST) → sirve otra página HTML completa
```

Sin JavaScript, la única forma de modificar algo en la página era recargarla entera. Esto cuesta ancho de banda del servidor.

### Dynamic HTML y Ajax

Con JavaScript podemos reemplazar/recargar partes del documento manipulando el DOM.

```
1ra request (GET) → sirve HTML + JavaScript
2da request (Ajax) → sirve solo un fragmento HTML
```

### Single Page Applications (SPAs)

En React, Angular, etc., la arquitectura cambió completamente:

```
1ra request (GET) → sirve solo un esqueleto HTML + bundle JS
El HTML completo se arma en el cliente con JavaScript
```

## DOM Tree

Cuando el navegador carga una página, crea el **Document Object Model** como un árbol. En este árbol todo es un **nodo** y cada nodo es un objeto.

### Tipos de nodos

| Nodo | Descripción |
|---|---|
| `Document` | El nodo documento (`document`) |
| `Element` | Elementos HTML: `<div>`, `<p>`, `<span>` |
| `Text` | Contenido textual de un elemento |
| `Comment` | Comentarios HTML `<!-- ... -->` |
| `Attr` | Atributos (`class="maia"`) |
| `DocumentFragment` | Fragmento de documento (útil para manipulación fuera del DOM) |
| `ShadowRoot` | Shadow DOM (Web Components) |

### Jerarquía de objetos

```
Object → EventTarget → Node → Element → HTMLElement → HTMLDocument
                                    → Document
                                    → Attr
                                    → Text
```

## Accediendo al DOM

### Métodos de acceso

| Método | Desde `document` | Desde un elemento | Retorno |
|---|---|---|---|
| `getElementById(id)` | ✅ | ❌ | Element (único) |
| `getElementsByTagName(tag)` | ✅ | ✅ | HTMLCollection (live) |
| `getElementsByClassName(class)` | ✅ | ✅ | HTMLCollection (live) |
| `getElementsByName(name)` | ✅ | ❌ | NodeList |
| `querySelector(selector)` | ✅ | ✅ | Element (único) |
| `querySelectorAll(selector)` | ✅ | ✅ | NodeList (static) |

```js
document.getElementById("app");
document.getElementsByClassName("card");
document.querySelector(".container p");
document.querySelectorAll("li");
element.getElementsByTagName("span");
```

## Modificando Contenido

### .textContent vs .innerText

| `.textContent` | `.innerText` |
|---|---|
| Devuelve todo el contenido textual, incluyendo `<script>` y `<style>` | Solo texto "humano" (respeta estilos) |
| No considera estilos | No devuelve texto de elementos ocultos (`display: none`) |
| Rápido — no trigger reflow | Lento — trigger reflow (caro) |

```js
const el = document.getElementById("content");
el.textContent = "Nuevo texto";
```

### .innerHTML

```js
element.innerHTML = "<p>Nuevo contenido</p>";
```

**⚠️ Peligro de seguridad (XSS):** si usas `innerHTML` con contenido desconocido (ej: de un CMS), estás implementando una vulnerabilidad XSS. Un atacante puede inyectar scripts que roben datos o inicien transacciones.

Aunque `<script>` no se ejecute directamente con `innerHTML`, hay formas alternativas de ejecutar código (ej: a través de errores en carga de imágenes). **Usa `textContent` siempre que puedas.**

### Atributos

```js
element.getAttribute("id");
element.setAttribute("id", "main");
element.hasAttribute("class");
element.removeAttribute("id");
```

**Atributos vs propiedades:** algunos atributos estándar se reflejan como propiedades en el objeto. Ej: `class` → `className`, `style` → objeto con propiedades en camelCase.

### .classList

```js
element.classList.add("active");
element.classList.remove("hidden");
element.classList.replace("old", "new");
element.classList.toggle("visible");  // on/off
element.classList.contains("active"); // true/false
```

## Modificando la Estructura del DOM

### createElement

```js
const div = document.createElement("div");
div.textContent = "Hola";
document.body.appendChild(div);
```

`createElement` crea un nuevo elemento pero **no lo agrega al DOM**. Hay que usar `appendChild`, `insertBefore`, etc.

### Métodos de inserción

```js
parent.appendChild(newNode);          // al final
parent.insertBefore(newNode, refNode); // antes de refNode
parent.replaceChild(newNode, oldNode); // reemplaza
parent.removeChild(childNode);         // elimina
```

### Ejemplo: mover un nodo

```js
const parent = document.getElementById("list");
const first = parent.firstElementChild;
const removed = parent.removeChild(first);
parent.insertBefore(removed, null); // lo mueve al final
```
