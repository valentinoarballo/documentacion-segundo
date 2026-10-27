# Tema 12. useContext

Con varias páginas (tema 11), el carrito debe ser accesible desde el catálogo, el detalle, el encabezado y la página de carrito. Pasar props por todos los niveles (*prop drilling*) se vuelve inmanejable. `useContext` permite compartir estado global. Este tema deja el carrito listo para conectarlo con el formulario de checkout del tema 13.

## Cuándo usar contexto

Para datos requeridos por muchos componentes no relacionados: carrito, usuario autenticado, tema visual. No es un reemplazo de las props para datos locales.

## Paso 1: crear el contexto y su Provider

```jsx
// src/context/CartContext.jsx
import { createContext, useContext, useEffect, useState } from "react";

const CartContext = createContext(null);

export function CartProvider({ children }) {
  const [items, setItems] = useState(
    () => JSON.parse(localStorage.getItem("carrito")) ?? []
  );

  useEffect(() => {
    localStorage.setItem("carrito", JSON.stringify(items));
  }, [items]);

  const agregar = (producto) =>
    setItems((prev) => {
      const existe = prev.find((i) => i.id === producto.id);
      return existe
        ? prev.map((i) => (i.id === producto.id ? { ...i, cantidad: i.cantidad + 1 } : i))
        : [...prev, { ...producto, cantidad: 1 }];
    });

  const quitar = (id) => setItems((prev) => prev.filter((i) => i.id !== id));
  const vaciar = () => setItems([]);

  const total = items.reduce((acc, i) => acc + i.precio * i.cantidad, 0);
  const cantidadTotal = items.reduce((acc, i) => acc + i.cantidad, 0);

  return (
    <CartContext.Provider value={{ items, agregar, quitar, vaciar, total, cantidadTotal }}>
      {children}
    </CartContext.Provider>
  );
}

export function useCart() {
  const context = useContext(CartContext);
  if (!context) throw new Error("useCart debe usarse dentro de CartProvider");
  return context;
}
```

Exportar el hook `useCart` simplifica el consumo y valida el uso correcto.

## Paso 2: envolver la aplicación

```jsx
// src/main.jsx
<BrowserRouter>
  <CartProvider>
    <App />
  </CartProvider>
</BrowserRouter>
```

## Paso 3: consumir el contexto

```jsx
// Header: contador del carrito
function Header() {
  const { cantidadTotal } = useCart();
  return <Link to="/carrito">Carrito ({cantidadTotal})</Link>;
}

// ProductCard: agregar
function ProductCard({ producto }) {
  const { agregar } = useCart();
  return <button onClick={() => agregar(producto)}>Agregar al carrito</button>;
}

// Página del carrito
function Carrito() {
  const { items, quitar, total } = useCart();
  return (
    <>
      <ul>
        {items.map((i) => (
          <li key={i.id}>
            {i.nombre} x{i.cantidad}
            <button onClick={() => quitar(i.id)}>Quitar</button>
          </li>
        ))}
      </ul>
      <p>Total: ${total.toFixed(2)}</p>
    </>
  );
}
```

## Consideración de rendimiento

Cuando cambia el valor del contexto, se vuelven a renderizar todos sus consumidores. Es recomendable separar contextos por responsabilidad (carrito, usuario, tema) en lugar de uno único con todo el estado.
