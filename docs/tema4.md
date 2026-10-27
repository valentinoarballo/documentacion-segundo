# Tema 4. Funciones

Las funciones encapsulan la lógica de la tienda para reutilizarla: impuestos, descuentos, envío y total del carrito. Se aplican los bucles y métodos de array del tema 3. Las funciones definidas aquí se usan en el tema 5 para dibujar el catálogo en la página.

## Funciones

### Declaración y expresión

```js
function calcularIva(precio) {
  return precio * 0.21;
}

const calcularIva2 = function (precio) {
  return precio * 0.21;
};
```

### Funciones flecha

```js
const conIva = (precio) => precio * 1.21;
const sumar = (a, b) => a + b;
```

### Parámetros por defecto y desestructuración

```js
const calcularTotal = (precio, cantidad = 1, descuento = 0) =>
  precio * cantidad * (1 - descuento);

const resumen = ({ nombre, precio }) => `${nombre}: $${precio}`;
console.log(resumen(producto));
```

### Funciones como argumentos (callbacks)

```js
const aplicarADos = (lista, fn) => lista.map(fn);

const conDescuento = aplicarADos(productos, (p) => ({
  ...p,
  precio: p.precio * 0.9,
}));
```

El operador spread (`...p`) copia las propiedades sin mutar el objeto original.

## Ejemplo integrador: total del carrito

```js
const carrito = [
  { id: 1, nombre: "Teclado mecánico", precio: 89.9, cantidad: 2 },
  { id: 3, nombre: "Monitor 24\"", precio: 210, cantidad: 1 },
];

const calcularEnvio = (subtotal) => (subtotal >= 200 ? 0 : 15);

function totalCarrito(items) {
  const subtotal = items.reduce((acc, i) => acc + i.precio * i.cantidad, 0);
  return subtotal + calcularEnvio(subtotal);
}

console.log(totalCarrito(carrito));
```
