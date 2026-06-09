# Frontend Optimization

## Introducción

Frontend Optimization (FEO) es el proceso de ajustar un sitio web para que sea más rápido y eficiente. Se enfoca en reducir el tamaño de archivos y minimizar el número de requests.

Hay dos tipos de tiempo de carga:
- **Perceived load time** — lo que el usuario percibe (impacta UX)
- **Actual load time** — métrica de rendimiento objetiva

## Render, Layout & Paint

Pasos del navegador al cargar una página:

1. HTML → **DOM tree**
2. CSS → **CSSOM tree**
3. DOM + CSSOM → **Render tree** (solo nodos visibles)
4. **Layout** (reflow) — calcula geometría de cada nodo
5. **Paint** — dibuja los nodos en pantalla

### Reflow & Repaint

| Acción | Reflow | Repaint |
|---|---|---|
| `display: none` | ✅ | ✅ |
| `visibility: hidden` | ❌ | ✅ |
| Cambiar color | ❌ | ✅ |
| Cambiar width/height | ✅ | ✅ |
| Animar, mover nodos | ✅ | ✅ |
| Redimensionar ventana | ✅ | ✅ |

### Cómo minimizar Reflows

- **Batch DOM changes** fuera del live DOM tree:
  - Usar `documentFragment`
  - Clonar el nodo, modificarlo, intercambiar
  - Ocultar con `display: none` (1 reflow), hacer cambios, mostrar (1 reflow)
- Cachear computed styles en variables locales
- Usar `position: absolute` — aísla el elemento del layout general

## HTML Optimization

- **Eliminar atributos innecesarios**: `type="text/javascript"` y `type="text/css"` ya no son necesarios
- **Minificar HTML**: espacios, comentarios y breaks innecesarios
- **CSS antes que JS**: las hojas de estilo deben cargarse antes del JavaScript

## CSS Optimization

| Técnica | Descripción |
|---|---|
| Concatenar | Combinar múltiples CSS en un archivo (menos válido con HTTP/2) |
| Minificar | Reducir tamaño eliminando whitespace |
| Preferir `<link>` sobre `@import` | `@import` bloquea el render |
| Simplificar selectores | Selectores complejos son más lentos |
| CSS Sprites | Combinar imágenes pequeñas en una sola |
| Animaciones en GPU | Usar `transform` y `opacity` (no animar propiedades que trigger reflow) |
| Evitar inline styles | No poner CSS en el `<body>` |
| Eliminar CSS no usado | Herramientas como PurifyCSS |

## Image Optimization

| Práctica | Descripción |
|---|---|
| **Usar CSS en vez de imágenes** | Gradientes, shadows, border-radius |
| **Usar SVG** | Escalable, tamaño pequeño |
| **Comprimir al 85%** | Balance calidad/tamaño |
| **Lazy loading** | `loading="lazy"` en imágenes |
| **srcset y picture** | Servir diferentes tamaños según dispositivo |
| **No usar imágenes para texto** | Headlines, direcciones, etc. deben ser texto real |

### Base64

**Pros:** ahorra HTTP requests.  
**Contras:** código ~30% más pesado, difícil de mantener. Solo usarlo en archivos fuertemente cacheados.

## JS Optimization

### Code Splitting

Dividir el bundle en múltiples piezas y enviar solo lo necesario para la ruta inicial:

```js
// Dynamic import — webpack crea un chunk separado
button.addEventListener("click", () => {
  import("./module.js").then(module => {
    module.doSomething();
  });
});
```

### Técnicas

| Técnica | Descripción |
|---|---|
| **Bundling** | Combinar JS en bundles |
| **Minification** | UglifyJS (ES5), babel-minify (ES2015+) |
| **Compression** | gzip (mínimo) |
| **Tree shaking** | Eliminar código no usado |
| **Code splitting** | Cargar solo lo necesario para cada ruta |
| **Lazy loading** | Diferir carga de código no crítico |
| **Caching** | `max-age`, ETag, Service Worker, long-term caching con hashes |
| **Async/Defer** | `async` o `defer` en scripts de terceros |
| **HTTP/2** | Multiplexing, server push |

### Third-party scripts

Si un script de tercero ralentiza la carga:
- Usar `async` o `defer`
- Self-hosting si el servidor es lento
- Considerar removerlo si no aporta valor
- Usar Resource Hints: `<link rel="preconnect">`, `<link rel="dns-prefetch">`
