# Tema 8. Componentes de React

Un componente es la unidad básica de una aplicación React. Se construyen los componentes del catálogo (tarjeta y lista de productos) y se estudian JSX, props, renderizado de listas y renderizado condicional. Parte del proyecto creado en el tema 7; en el tema 9 los componentes incorporan estado y datos desde una API.

## Componentes y JSX

Un componente es una función que devuelve JSX. Su nombre comienza con mayúscula.

```jsx
// src/components/ProductCard.jsx
export default function ProductCard({ nombre, precio, stock }) {
  return (
    <article className="tarjeta">
      <h2>{nombre}</h2>
      <p>${precio}</p>
      <button disabled={stock === 0}>
        {stock > 0 ? "Agregar al carrito" : "Agotado"}
      </button>
    </article>
  );
}
```

Diferencias de JSX respecto de HTML: `className` en lugar de `class`, atributos en camelCase, etiquetas siempre cerradas y expresiones JavaScript entre llaves `{}`.

## Props

Las props son los datos que un componente padre entrega a su hijo. Son de **solo lectura**.

```jsx
<ProductCard nombre="Teclado mecánico" precio={89.9} stock={12} />
```

## Renderizado de listas

Cada elemento de una lista requiere una `key` estable y única.

```jsx
// src/components/ProductList.jsx
import ProductCard from "./ProductCard";

export default function ProductList({ productos }) {
  return (
    <section className="grid">
      {productos.map((p) => (
        <ProductCard key={p.id} {...p} />
      ))}
    </section>
  );
}
```

## Renderizado condicional

```jsx
{productos.length === 0 ? <p>No hay productos.</p> : <ProductList productos={productos} />}
{oferta && <span className="badge">Oferta</span>}
```

## `children`

Permite componer componentes contenedores.

```jsx
function Panel({ titulo, children }) {
  return (
    <section>
      <h2>{titulo}</h2>
      {children}
    </section>
  );
}

<Panel titulo="Destacados">
  <ProductList productos={destacados} />
</Panel>
```

## Ensamblado

```jsx
// src/App.jsx
import ProductList from "./components/ProductList";

const productos = [
  { id: 1, nombre: "Teclado mecánico", precio: 89.9, stock: 12 },
  { id: 2, nombre: "Mouse inalámbrico", precio: 35, stock: 0 },
];

export default function App() {
  return (
    <main>
      <h1>TechShop</h1>
      <ProductList productos={productos} />
    </main>
  );
}
```
