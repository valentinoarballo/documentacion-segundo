# Tema 2. Variables, bloques y scope

Se estudian las formas de declarar variables, los bloques de código y el scope, junto con las estructuras condicionales que toman decisiones dentro de un bloque. Se aplican a las reglas de negocio de la tienda: stock y costo de envío. Retoma los tipos de datos del tema 1 y prepara el tema 3, donde se recorren colecciones de productos.

## Declaración de variables

| Palabra | Reasignable | Scope | Uso recomendado |
|---------|-------------|-------|-----------------|
| `const` | No | Bloque | Valor por defecto |
| `let` | Sí | Bloque | Valores que cambian |
| `var` | Sí | Función | Evitar |

```js
const nombreTienda = "TechShop";
let cantidadEnCarrito = 0;

cantidadEnCarrito = cantidadEnCarrito + 1;
nombreTienda = "Otra"; // TypeError: Assignment to constant variable
```

## Scope

El **scope** define dónde es visible una variable.

```js
const iva = 0.21; // global

function precioFinal(precio) {
  const total = precio * (1 + iva); // local a la función
  return total;
}

if (true) {
  let mensaje = "Dentro del bloque";
}
console.log(mensaje); // ReferenceError
```

Regla práctica: declarar siempre en el scope más reducido posible y preferir `const`.

## Hoisting y zona temporal muerta

Las declaraciones se registran al inicio de su scope. Con `var` la variable existe antes de su línea con valor `undefined`; con `let` y `const` no se puede usar antes de declararla.

```js
console.log(a); // undefined
var a = 1;

console.log(b); // ReferenceError
let b = 1;
```

## Bloques y condicionales

Un bloque es el código entre llaves `{}`. Las estructuras condicionales ejecutan un bloque según una condición.

```js
function estadoStock(stock) {
  if (stock === 0) {
    return "Sin stock";
  } else if (stock < 5) {
    return "Últimas unidades";
  }
  return "Disponible";
}
```

### Operador ternario

```js
const etiqueta = producto.stock > 0 ? "Agregar al carrito" : "Agotado";
```

### `switch`

```js
function descuentoPorCategoria(categoria) {
  switch (categoria) {
    case "perifericos":
      return 0.1;
    case "monitores":
      return 0.05;
    default:
      return 0;
  }
}
```

### Valores falsy y operadores de control

Son falsy: `false`, `0`, `""`, `null`, `undefined`, `NaN`.

```js
const cupon = null;
const codigo = cupon ?? "SIN_CUPON";           // solo null/undefined
const marca = producto.marca || "Genérica";    // cualquier falsy
const ciudad = usuario?.direccion?.ciudad;     // optional chaining
```
