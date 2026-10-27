# Tema 5. DOM y eventos

Aquí JavaScript empieza a interactuar con la página. Se estudia el DOM para renderizar el catálogo de la tienda y los eventos para responder a las acciones del usuario, como agregar productos al carrito o buscar. Se apoya en las funciones del tema 4; en el tema 6 los datos pasan a venir de una API y el carrito se vuelve persistente.

## El árbol del DOM

El navegador convierte el HTML en un árbol de nodos accesible desde `document`.

```html
<body>
  <h1>TechShop</h1>
  <section id="catalogo" class="grid"></section>
  <script type="module" src="src/main.js"></script>
</body>
```

## Seleccionar elementos

```js
const catalogo = document.getElementById("catalogo");
const titulo = document.querySelector("h1");
const tarjetas = document.querySelectorAll(".tarjeta"); // NodeList
```

Se recomienda `querySelector` y `querySelectorAll` por usar selectores CSS.

## Modificar contenido y atributos

```js
titulo.textContent = "TechShop - Catálogo";
titulo.classList.add("destacado");
titulo.style.color = "#0a7";
catalogo.setAttribute("aria-label", "Listado de productos");
```

`textContent` inserta texto plano. `innerHTML` interpreta HTML: no usarlo con datos que provengan de usuarios, por riesgo de XSS.

## Crear y agregar elementos

```js
function crearTarjeta(producto) {
  const tarjeta = document.createElement("article");
  tarjeta.className = "tarjeta";

  const nombre = document.createElement("h2");
  nombre.textContent = producto.nombre;

  const precio = document.createElement("p");
  precio.textContent = `$${producto.precio}`;

  const boton = document.createElement("button");
  boton.textContent = "Agregar al carrito";
  boton.dataset.id = producto.id;
  boton.disabled = producto.stock === 0;

  tarjeta.append(nombre, precio, boton);
  return tarjeta;
}

productos.forEach((p) => catalogo.appendChild(crearTarjeta(p)));
```

## Renderizado con template literals

Más compacto para estructuras simples:

```js
catalogo.innerHTML = productos
  .map(
    (p) => `
    <article class="tarjeta">
      <h2>${p.nombre}</h2>
      <p>$${p.precio}</p>
      <button data-id="${p.id}" ${p.stock === 0 ? "disabled" : ""}>
        Agregar al carrito
      </button>
    </article>`
  )
  .join("");
```

## Eliminar y reemplazar

```js
tarjeta.remove();
catalogo.replaceChildren(); // vacía el contenedor
```

## Patrón recomendado

Separar los datos de su representación: una función `renderCatalogo(lista)` que limpia el contenedor y vuelve a dibujar. Este patrón se retoma en React, donde la interfaz es función del estado.

```js
function renderCatalogo(lista) {
  catalogo.replaceChildren(...lista.map(crearTarjeta));
}
renderCatalogo(productos);
```

## `addEventListener`

```js
const boton = document.querySelector("button");

boton.addEventListener("click", (event) => {
  console.log("Clic en", event.target);
});
```

Eventos frecuentes: `click`, `input`, `change`, `submit`, `keydown`, `mouseover`, `DOMContentLoaded`.

## El objeto `event`

| Propiedad | Uso |
|-----------|-----|
| `event.target` | Elemento que originó el evento |
| `event.currentTarget` | Elemento que tiene el listener |
| `event.preventDefault()` | Cancela el comportamiento por defecto |
| `event.key` | Tecla presionada (`keydown`) |

## Delegación de eventos

Con muchos productos, registrar un listener por botón es ineficiente y no funciona con elementos creados después. Se registra uno solo en el contenedor.

```js
const carrito = [];

catalogo.addEventListener("click", (event) => {
  const boton = event.target.closest("button[data-id]");
  if (!boton) return;

  const id = Number(boton.dataset.id);
  agregarAlCarrito(id);
});

function agregarAlCarrito(id) {
  const producto = productos.find((p) => p.id === id);
  const existente = carrito.find((i) => i.id === id);

  if (existente) {
    existente.cantidad++;
  } else {
    carrito.push({ ...producto, cantidad: 1 });
  }
  renderCarrito();
}
```

## Renderizar el carrito

```js
const listaCarrito = document.getElementById("carrito");
const totalEl = document.getElementById("total");

function renderCarrito() {
  listaCarrito.innerHTML = carrito
    .map((i) => `<li>${i.nombre} x${i.cantidad} - $${i.precio * i.cantidad}</li>`)
    .join("");
  totalEl.textContent = totalCarrito(carrito);
}
```

## Buscador con evento `input`

```js
const buscador = document.getElementById("buscador");

buscador.addEventListener("input", (event) => {
  const texto = event.target.value.toLowerCase();
  const filtrados = productos.filter((p) =>
    p.nombre.toLowerCase().includes(texto)
  );
  renderCatalogo(filtrados);
});
```

## Formularios con `submit`

```js
document.getElementById("form-cupon").addEventListener("submit", (event) => {
  event.preventDefault();
  const codigo = new FormData(event.target).get("codigo");
  console.log("Cupón ingresado:", codigo);
});
```

## Propagación

Los eventos suben por el árbol (*bubbling*): un clic en un botón también dispara los listeners de sus ancestros. `event.stopPropagation()` lo detiene, aunque conviene usarlo solo cuando sea imprescindible.
