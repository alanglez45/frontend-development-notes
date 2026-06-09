# Unit Testing

## Qué es un Unit Test

Es código que evalúa el resultado de otro código específico. Un **unit** (unidad) suele ser una función o método.

```
test case → unit (SUT) → result → compare with expected value
```

Un unit test debe ser:
- **Reproducible** — mismo resultado siempre, sin importar tiempo o condiciones externas
- **Consistente** — mismo resultado en cualquier entorno
- **Rápido** — miles de tests deben correr antes de cada commit

## FE Unit Testing es más complejo

El código Frontend no es solo core JavaScript:
- Usa DOM, browser APIs
- El resultado puede ser un cambio en el DOM
- Los tests corren en Node.js, no en el navegador → necesita JSDOM o headless browser

### Testing Frameworks

| Framework | Usado con |
|---|---|
| **Jest** | React (Facebook) |
| **Jasmine** | Angular (battle-hardened) |
| **Karma** | Host para tests en browsers reales |
| **Mocha** | Opción flexible |

## Estructura básica (Jasmine/Jest)

```js
describe("Calculator", () => {
  it("should add two numbers", () => {
    expect(add(2, 3)).toBe(5);
  });
});
```

- `describe` — agrupa tests (puede anidarse)
- `it`/`test` — un caso de test
- `expect` — assertion

## Cómo funciona un testing framework por dentro

`describe` e `it` solo **registran** tests. Las assertions comparan valores y registran resultados. Los tests pueden correr en paralelo — el orden de ejecución no es fijo.

## Working with Jest

```bash
npm init -y
npm i jest
# en package.json: "test": "jest"
npm test
```

Para soporte de ES Modules:

```bash
npm i babel-jest @babel/core @babel/preset-env
```

## Coverage

```bash
npm test -- --coverage
```

Jest usa **istanbul** para generar reportes de cobertura: statements, branches, functions, lines.

## Reglas para buenos unit tests

### Regla I — Siempre testea la API pública

Las funciones/métodos privados son internos del módulo. Si los tests dependen de internals, son frágiles — cualquier refactor rompe los tests.

```js
// Bien: testear la función pública
describe("add", () => {
  it("sums two numbers", () => {
    expect(add(2, 3)).toBe(5);
  });
});
```

### Regla II — No mockear partes del SUT

Si sientes la necesidad de mockear una función dentro del mismo módulo, esa función debería extraerse a un módulo independiente.

### Regla III — Un test solo testea la responsabilidad de la unidad

Si quieres testear un banana, no testees el gorila que sostiene el banana y toda la selva. Mockea las dependencias para aislar la unidad.

### Regla IV — No expongas los internals de la unidad en el test

| Ocultar internals (bueno) | Exponer internals (malo) |
|---|---|
| Refactor no rompe tests | Refactor rompe tests aunque no haya bugs |
| Testea comportamiento externo (API pública) | Testea estructura interna |
| Mocking imposible en algunos casos | Mocking necesario (API calls, etc.) |

## Mocks, Spies y restore

```js
// Mock con jest.fn()
const mockFn = jest.fn();
mockFn.mockReturnValue(10);

// Spy — mock + restauración automática
jest.spyOn(mathPrimitives, 'addNumbers').mockReturnValue(10);

// Restaurar mocks
beforeEach(() => {
  jest.restoreAllMocks();
});
```

Los mocks deben restaurarse. Si no se restauran, el mock "filtra" a otros tests. Usar `jest.restoreAllMocks()` en `beforeEach` es más seguro que restaurar manualmente (que falla si un expect falla antes).
