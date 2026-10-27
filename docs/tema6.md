# Tema 6. Fetch y LocalStorage

Este tema reemplaza los datos escritos a mano por datos de una API mediante `fetch`, y hace persistente el carrito con Web Storage. Con esto se cierra el bloque de JavaScript; el tema 7 inicia el trabajo con React sobre la misma tienda.

## Asincronía: Promesas y `async/await`

Una petición de red tarda un tiempo indeterminado. `fetch` devuelve una **promesa**, que `async/await` permite leer de forma secuencial.

```js
const respuesta = await fetch("https://dummyjson.com/products");
const datos = await respuesta.json();
console.log(datos.products);
```

## Función de carga con manejo de errores

`fetch` solo rechaza la promesa ante fallos de red; un estado 404 o 500 no lanza error, por lo que se verifica `response.ok`.

```js
const API = "https://dummyjson.com";

async function obtenerProductos() {
  const respuesta = await fetch(`${API}/products?limit=12`);

  if (!respuesta.ok) {
    throw new Error(`Error HTTP ${respuesta.status}`);
  }

  const { products } = await respuesta.json();
  return products;
}
```

## Integrar con el catálogo

```js
async function iniciar() {
  catalogo.textContent = "Cargando productos...";
  try {
    const productos = await obtenerProductos();
    renderCatalogo(productos);
  } catch (error) {
    catalogo.textContent = "No se pudieron cargar los productos.";
    console.error(error);
  }
}

iniciar();
```

Los nombres de campos de la API (`title`, `price`) pueden diferir de los del modelo propio. Se normalizan en un único lugar:

```js
const normalizar = (p) => ({
  id: p.id,
  nombre: p.title,
  precio: p.price,
  stock: p.stock,
  imagen: p.thumbnail,
});

const productos = (await obtenerProductos()).map(normalizar);
```

## Detalle de un producto

```js
async function obtenerProducto(id) {
  const respuesta = await fetch(`${API}/products/${id}`);
  if (!respuesta.ok) throw new Error("Producto no encontrado");
  return normalizar(await respuesta.json());
}
```

## Enviar datos: POST

```js
async function crearPedido(pedido) {
  const respuesta = await fetch(`${API}/carts/add`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(pedido),
  });
  return respuesta.json();
}

await crearPedido({ userId: 1, products: [{ id: 1, quantity: 2 }] });
```

## Peticiones en paralelo

```js
const [productos, categorias] = await Promise.all([
  fetch(`${API}/products`).then((r) => r.json()),
  fetch(`${API}/products/categories`).then((r) => r.json()),
]);
```

## Web Storage

| | `localStorage` | `sessionStorage` |
|---|---|---|
| Duración | Hasta que se borre | Hasta cerrar la pestaña |
| Alcance | Mismo origen, todas las pestañas | Mismo origen, solo esa pestaña |
| Capacidad | ~5 MB | ~5 MB |
| Uso en la tienda | Carrito, tema visual | Filtros de la sesión actual |

Solo almacena **strings**.

## API básica

```js
localStorage.setItem("tema", "oscuro");
localStorage.getItem("tema");     // "oscuro"
localStorage.removeItem("tema");
localStorage.clear();
```

## Guardar objetos: JSON

```js
localStorage.setItem("carrito", JSON.stringify(carrito));

const guardado = JSON.parse(localStorage.getItem("carrito"));
```

## Persistir el carrito

Se centraliza el acceso en dos funciones y se manejan los errores, ya que `JSON.parse` falla con datos corruptos y el storage puede no estar disponible.

```js
const CLAVE = "carrito";

function guardarCarrito(carrito) {
  try {
    localStorage.setItem(CLAVE, JSON.stringify(carrito));
  } catch (error) {
    console.error("No se pudo guardar el carrito", error);
  }
}

function cargarCarrito() {
  try {
    return JSON.parse(localStorage.getItem(CLAVE)) ?? [];
  } catch {
    return [];
  }
}

let carrito = cargarCarrito();
```

Se llama a `guardarCarrito(carrito)` al final de cada cambio:

```js
function agregarAlCarrito(id) {
  // ...lógica del tema 6
  guardarCarrito(carrito);
  renderCarrito();
}
```

## SessionStorage para filtros

```js
buscador.addEventListener("input", (e) => {
  sessionStorage.setItem("busqueda", e.target.value);
});

buscador.value = sessionStorage.getItem("busqueda") ?? "";
```

## Consideraciones

- No guardar datos sensibles (contraseñas, tarjetas, tokens de larga duración).
- Cualquier script del mismo origen puede leer el storage.
- Los valores son strings: convertir tipos al leer.
