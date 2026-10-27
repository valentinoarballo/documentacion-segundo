# Tema 9. useState y useEffect

Los componentes del tema 8 muestran datos fijos. Los hooks permiten que un componente tenga memoria (`useState`) y ejecute efectos secundarios, como pedir datos a una API (`useEffect`). Se reemplaza el `fetch` del tema 6 por `axios` y se hace dinámico el carrito. El tema 10 divide la aplicación en páginas.

## `useState`

```jsx
import { useState } from "react";

function Contador({ stock }) {
  const [cantidad, setCantidad] = useState(1);

  return (
    <div>
      <button onClick={() => setCantidad((c) => Math.max(1, c - 1))}>-</button>
      <span>{cantidad}</span>
      <button onClick={() => setCantidad((c) => Math.min(stock, c + 1))}>+</button>
    </div>
  );
}
```

Reglas:

- El estado no se modifica directamente: se usa siempre la función `set`.
- Para arrays y objetos se crean copias nuevas (`[...items, nuevo]`).
- Si el nuevo valor depende del anterior, usar la forma funcional `setX(prev => ...)`.

## Carrito con estado

```jsx
function App() {
  const [carrito, setCarrito] = useState([]);

  const agregar = (producto) => {
    setCarrito((prev) => {
      const existe = prev.find((i) => i.id === producto.id);
      return existe
        ? prev.map((i) => (i.id === producto.id ? { ...i, cantidad: i.cantidad + 1 } : i))
        : [...prev, { ...producto, cantidad: 1 }];
    });
  };

  const total = carrito.reduce((acc, i) => acc + i.precio * i.cantidad, 0);
  // ...
}
```

## `useEffect`

Ejecuta código luego del renderizado. El arreglo de dependencias controla cuándo se repite.

```jsx
useEffect(() => { /* en cada render */ });
useEffect(() => { /* solo al montar */ }, []);
useEffect(() => { /* cuando cambia "categoria" */ }, [categoria]);
```

Persistencia del carrito (continuación del tema 7):

```jsx
const [carrito, setCarrito] = useState(
  () => JSON.parse(localStorage.getItem("carrito")) ?? []
);

useEffect(() => {
  localStorage.setItem("carrito", JSON.stringify(carrito));
}, [carrito]);
```

## axios

```bash
npm install axios
```

Se centraliza la configuración en una instancia:

```jsx
// src/api/client.js
import axios from "axios";

export const api = axios.create({
  baseURL: "https://dummyjson.com",
  timeout: 8000,
});
```

Diferencias con `fetch`: parsea JSON automáticamente (`response.data`), rechaza la promesa ante estados 4xx/5xx y admite interceptores.

## Cargar productos con estados de carga y error

```jsx
function useProductos(categoria) {
  const [productos, setProductos] = useState([]);
  const [cargando, setCargando] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();
    setCargando(true);

    api
      .get(categoria ? `/products/category/${categoria}` : "/products", {
        signal: controller.signal,
      })
      .then((res) => setProductos(res.data.products))
      .catch((err) => {
        if (!axios.isCancel(err)) setError(err.message);
      })
      .finally(() => setCargando(false));

    return () => controller.abort(); // limpieza
  }, [categoria]);

  return { productos, cargando, error };
}
```

Uso:

```jsx
const { productos, cargando, error } = useProductos();

if (cargando) return <p>Cargando...</p>;
if (error) return <p>Error: {error}</p>;
return <ProductList productos={productos} />;
```

La función retornada por el efecto se ejecuta al desmontar el componente o antes de repetirse: evita actualizar estado con respuestas obsoletas.
