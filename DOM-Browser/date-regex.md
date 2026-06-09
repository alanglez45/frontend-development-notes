# Date & Regular Expressions

## Date

### Unix Epoch

JavaScript maneja fechas contando **milisegundos** desde el Unix Epoch: **1 de enero de 1970, 00:00:00 UTC**.

```js
const now = new Date();
console.log(now.getTime()); // milisegundos desde epoch
```

A diferencia de Unix timestamp (que usa segundos), JavaScript usa milisegundos.

### El problema de las fechas en JS

JavaScript Date es considerado una de las peores partes del lenguaje. Fue copiado de `java.util.Date` (deprecado en 1997) y no puede arreglarse sin romper la web.

El problema principal: **time zones**. Una fecha como "medianoche a medianoche" es ambigua — ¿zona horaria del cliente? ¿de la sede? ¿del servidor? ¿de la base de datos? Como developer, terminas manteniendo la sincronización de tiempos entre todos estos.

### Date Constructor

```js
new Date();                    // ahora
new Date("2024-01-01");       // string
new Date(2024, 0, 1);         // year, month (0-indexed), day
new Date(2024, 0, 1, 12, 30); // + hora, minutos
new Date(1704067200000);      // milliseconds

Date(); // sin new → devuelve string
```

**Importante:** los meses empiezan en **0** (enero = 0, diciembre = 11), los días empiezan en **1**. Ambos pueden ser negativos.

### Date Methods

| Categoría | Métodos |
|---|---|
| Extraer | `getFullYear()`, `getMonth()`, `getDate()`, `getDay()`, `getHours()`, `getMinutes()`, `getSeconds()`, `getMilliseconds()` |
| Manipular | `setFullYear()`, `setMonth()`, `setDate()`, `setHours()`, etc. |
| Timestamp | `getTime()`, `valueOf()`, `Date.now()` |
| Formatear | `toString()`, `toISOString()`, `toUTCString()`, `toLocaleString()`, `toGMTString()` |

```js
const date = new Date();

// Extraer
date.getFullYear();  // 2024
date.getMonth();     // 0-11
date.getDate();      // 1-31
date.getDay();       // 0-6 (domingo=0)

// Timestamp
date.getTime();      // ms desde epoch
Date.now();          // lo mismo, estático

// Formatear
date.toISOString();  // "2024-01-15T10:30:00.000Z"
date.toLocaleString("es-MX"); // formato local
```

## Regular Expressions

Las **regex** son patrones para buscar, extraer y reemplazar combinaciones de caracteres en strings. En JS, `RegExp` es un built-in object.

### Creación

```js
// Regex literal (recomendado)
const regex = /pattern/flags;

// Constructor (útil para crear regex dinámicamente)
const regex = new RegExp("pattern", "flags");
```

Usa el constructor solo si necesitas crear la regex en **runtime** (ej: el patrón viene de un input del usuario). En cualquier otro caso, usa el literal.

### Flags

| Flag | Descripción |
|---|---|
| `g` | Global — busca todas las coincidencias, no solo la primera |
| `i` | Ignore case — no distingue mayúsculas/minúsculas |
| `m` | Multiline — `^` y `$`匹配 el inicio/fin de cada línea |
| `y` | Sticky — busca desde la posición actual |
| `u` | Unicode — trata el patrón como Unicode |
| `s` | DotAll — `.`匹配 cualquier carácter, incluyendo newline |

### Métodos de RegExp

| Método | Descripción |
|---|---|
| `regex.test(string)` | Devuelve `true`/`false` si hay match |
| `regex.exec(string)` | Devuelve un array con el match (o `null`) |

`RegExp` es **stateful** — con el flag `g`, `exec()` recuerda la posición (`lastIndex`) y continúa desde ahí en cada llamada.

### Métodos de String con Regex

| Método | Descripción |
|---|---|
| `string.match(regex)` | Array con matches (o `null`) |
| `string.matchAll(regex)` | Iterator con todos los matches (requiere flag `g`) |
| `string.replace(regex, replacement)` | Reemplaza match(es) |
| `string.replaceAll(regex, replacement)` | Reemplaza todos (requiere flag `g`) |
| `string.search(regex)` | Índice del primer match (o -1) |
| `string.split(regex)` | Divide el string por el patrón |

```js
const str = "The quick brown fox";

str.match(/fox/);         // ["fox"]
str.match(/[aeiou]/gi);   // ["e", "u", "i", "o", "o"]
str.replace(/fox/, "cat"); // "The quick brown cat"
str.split(/\s+/);         // ["The", "quick", "brown", "fox"]
```

### Quantifiers

| Símbolo | Significado |
|---|---|
| `x*` | 0 o más veces |
| `x+` | 1 o más veces |
| `x?` | 0 o 1 vez |
| `x{n}` | Exactamente n veces |
| `x{n,}` | Al menos n veces |
| `x{n,m}` | Entre n y m veces |

### Groups and Ranges

| Símbolo | Significado |
|---|---|
| `x\|y` | x o y |
| `[xyz]` / `[a-c]` | Character class — cualquiera de los caracteres |
| `[^xyz]` / `[^a-c]` | Negated character class — cualquier carácter **excepto** esos |
| `(x)` | Capturing group — match y recuerda |

### Character Classes

| Símbolo | Significado |
|---|---|
| `.` | Cualquier carácter (excepto newline, a menos que tenga flag `s`) |
| `\d` | Dígito (`[0-9]`) |
| `\D` | No dígito (`[^0-9]`) |
| `\w` | Word char (`[A-Za-z0-9_]`) |
| `\W` | No word char (`[^A-Za-z0-9_]`) |
| `\s` | Whitespace (espacio, tab, form feed, line feed) |
| `\S` | No whitespace |

### Assertions

| Símbolo | Significado |
|---|---|
| `^` | Inicio del input |
| `$` | Final del input |

### Ejemplo completo

```js
// Validar email
const emailRegex = /^[\w.-]+@[\w.-]+\.\w{2,}$/;
emailRegex.test("user@example.com"); // true

// Extraer números de un string
const nums = "abc123def456".match(/\d+/g); // ["123", "456"]

// Reemplazar espacios múltiples
"a   b   c".replace(/\s+/g, " "); // "a b c"

// URL params
const url = "?name=john&age=30";
const params = url.match(/[?&](\w+)=(\w+)/g);
```
