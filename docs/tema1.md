# Tema 1. Introducción a JavaScript

Este tema presenta JavaScript, su origen y los elementos básicos del lenguaje: tipos de datos, objetos, arrays y operadores. Con ellos se define el catálogo de productos de la tienda que se usa durante toda la materia. El tema 2 profundiza en cómo se declaran y acotan las variables.

## Qué es JavaScript

Lenguaje interpretado y de tipado dinámico, creado en 1995 para dar interactividad al navegador. Hoy corre también en servidores (Node.js). Su estándar se llama **ECMAScript**; desde ES6 (2015) incorpora `let`, `const`, funciones flecha, módulos y clases.

## Ejecutar código

Desde el navegador (consola de DevTools) o con Node:

```bash
node src/main.js
```

```js
console.log("Tienda iniciada");
```

## Tipos de datos

```js
const nombre = "Teclado mecánico"; // string
const precio = 89.9;               // number
const disponible = true;           // boolean
const descuento = null;            // ausencia intencional de valor
let marca;                         // undefined

console.log(typeof precio); // "number"
```

## Objetos y arrays

El catálogo es un array de objetos. Esta estructura se reutiliza en todos los temas.

```js
const producto = {
  id: 1,
  nombre: "Teclado mecánico",
  precio: 89.9,
  stock: 12,
};

const productos = [
  producto,
  { id: 2, nombre: "Mouse inalámbrico", precio: 35, stock: 0 },
  { id: 3, nombre: "Monitor 24\"", precio: 210, stock: 5 },
];

console.log(productos[0].nombre);
```

Aunque `producto` es `const`, sus propiedades se pueden modificar: `const` impide reasignar la variable, no mutar el objeto.

```js
producto.stock = producto.stock - 1;
```

## Template literals

```js
console.log(`${producto.nombre} - $${producto.precio}`);
```

## Operadores aritméticos

```js
const precio = 100;
const cantidad = 3;

const subtotal = precio * cantidad; // 300
const iva = subtotal * 0.21;        // 63
const total = subtotal + iva;       // 363
const resto = 10 % 3;               // 1
const cuadrado = 2 ** 2;            // 4
```

Incremento y asignación compuesta:

```js
let items = 0;
items++;       // 1
items += 2;    // 3
```

## Operadores de comparación y lógicos

Usar siempre igualdad estricta (`===`), que no convierte tipos.

```js
console.log(5 == "5");  // true
console.log(5 === "5"); // false

const hayStock = producto.stock > 0;
const esOferta = producto.precio < 50 && hayStock;
const envioGratis = total > 100 || esOferta;
```
