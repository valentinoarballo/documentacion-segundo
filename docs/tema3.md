# Tema 3. Bucles

Los bucles permiten recorrer el catálogo y el carrito para mostrarlos o calcular totales. Se ven los bucles clásicos y los métodos de array, que son la forma habitual de trabajar con colecciones en JavaScript moderno. Usa las variables y condicionales del tema 2; en el tema 4 esa lógica se encapsula en funciones reutilizables.

## Bucle `for`

```js
for (let i = 0; i < productos.length; i++) {
  console.log(productos[i].nombre);
}
```

## Bucle `for...of`

Recorre los valores de un array con una sintaxis más clara.

```js
let totalCarrito = 0;

for (const producto of productos) {
  totalCarrito += producto.precio;
}
console.log(totalCarrito);
```

## Bucle `for...in`

Recorre las **claves** de un objeto.

```js
const producto = { nombre: "Teclado mecánico", precio: 89.9, stock: 12 };

for (const clave in producto) {
  console.log(`${clave}: ${producto[clave]}`);
}
```

## `while`

Útil cuando no se conoce la cantidad de iteraciones.

```js
let descuentoAplicado = 0;
while (totalCarrito - descuentoAplicado > 200) {
  descuentoAplicado += 10;
}
```

## Métodos de array

Para transformar datos se prefieren los métodos de array por sobre los bucles manuales.

```js
// forEach: ejecutar una acción por elemento
productos.forEach((p) => console.log(p.nombre));

// map: transformar y devolver un nuevo array
const nombres = productos.map((p) => p.nombre);

// filter: quedarse con los que cumplen una condición
const disponibles = productos.filter((p) => p.stock > 0);

// reduce: acumular un valor
const total = productos.reduce((acc, p) => acc + p.precio, 0);

// find: devolver el primer elemento que coincide
const monitor = productos.find((p) => p.id === 3);
```
