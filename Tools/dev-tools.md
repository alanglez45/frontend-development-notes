# Dev Tools

## Package Managers

Un **package manager** gestiona las dependencias (código externo) que tu proyecto necesita. El archivo `package.json` guarda el registro de estas dependencias y metadatos del proyecto.

### npm vs Yarn

| npm | Yarn |
|---|---|
| Por defecto con Node.js | Necesita instalación aparte |
| `package-lock.json` | `yarn.lock` |
| `npm audit` para seguridad | Ultra fast, offline mode |
| Lento en versiones viejas | Deterministic, flat mode |
| Workspaces vía Lerna | Workspaces nativos |

### package.json — version ranges

| Símbolo | Significado |
|---|---|
| `1.2.3` | Exacta |
| `~1.2.3` | Aproximada (solo patch) |
| `^1.2.3` | Compatible (minor y patch) |
| `>1.2.3` | Mayor que |
| `>=1.2.3` | Mayor o igual |
| `<1.2.3` | Menor que |
| `<=1.2.3` | Menor o igual |

## Task Runners

Automatizan tareas repetitivas del build process: compilar Sass, minificar JS, optimizar imágenes, concatenar archivos, etc.

| Herramienta | Descripción |
|---|---|
| **Gulp** | Pipeline basado en streams. `gulp.src()` → loaders → `gulp.dest()` |
| **Grunt** | Configuración basada en objetos (JSON) |
| **npm scripts** | Simple, suficiente para muchos proyectos |

### Gulp API

```js
gulp.src(glob)       // crear stream desde archivos
gulp.dest(folder)    // guardar archivos del stream
gulp.task(name, fn)  // definir tarea
gulp.watch(glob, tasks) // ejecutar tarea al cambiar archivos
```

### Pipelines comunes

- **JS pipeline**: concat → uglify → sourcemaps
- **CSS pipeline**: sass/less → minify → autoprefixer
- **Assets**: optimizar imágenes, procesar templates
- **Watch**: live reload, rebuild automático
- **Test**: Karma, Mocha, coverage

## Module Bundlers

Empaquetan todos los módulos JS (y otros assets como imágenes, fuentes, CSS) en uno o más bundles.

### Webpack

```js
// webpack.config.js
module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.[contenthash].js"
  },
  module: {
    rules: [
      { test: /\.css$/, use: ["style-loader", "css-loader"] }
    ]
  },
  plugins: [],
  resolve: {
    alias: { "@": path.resolve(__dirname, "src") }
  }
};
```

**Loaders** transforman archivos antes de bundlear (CSS, imágenes, TypeScript, etc.). Se aplican de derecha a izquierda.

## Transpilers

Convierten código de un lenguaje a otro (source-to-source compilers).

| Herramienta | Descripción |
|---|---|
| **Babel** | Transpila ES6+/TypeScript a ES5 para navegadores viejos |
| **TypeScript** | Superset de JS con tipos, compila a JS |

## Linters

Analizan código estáticamente para mantener estilo, encontrar bugs potenciales y problemas de performance.

| Herramienta | Pros | Contras |
|---|---|---|
| **JSLint** | Listo para usar | Poco configurable, sin reglas custom |
| **JSHint** | Config file, soporte librerías | Sin soporte reglas custom, output confuso |
| **JSCS** | Custom reporters, presets | Solo estilo, no detecta bugs |
| **ESLint** | Más flexible, extensible, mejores reglas, mejor soporte ES6 | Requiere configuración |

ESLint es el estándar actual.

## Documentation

| Herramienta | Descripción |
|---|---|
| **Swagger** | Describe APIs REST, language-agnostic, legible por humanos y máquinas |
| **JSDoc** | Anotaciones en JS para generar documentación en HTML/RTF |

## Debugging & Extensions

| Herramienta | Descripción |
|---|---|
| **Chrome DevTools** | Debug paso a paso, network, performance, etc. |
| **Node Inspect** | Debug para Node.js (similar a Chrome DevTools) |
| **Augury** | Debug y profiling para Angular |
| **Redux DevTools** | Time-travel debugging para Redux |
