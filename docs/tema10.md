# Tema 10. Rutas base con React Router DOM

Una ecommerce necesita varias pantallas: inicio, catálogo y carrito. React Router permite navegar entre ellas sin recargar el navegador. Este tema instala la librería y define las rutas base de la tienda; en el tema 11 se agregan rutas con parámetros y rutas anidadas.

## Instalación

```bash
npm install react-router-dom
```

## Rutas base

```jsx
// src/main.jsx
import { BrowserRouter } from "react-router-dom";

ReactDOM.createRoot(document.getElementById("root")).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

```jsx
// src/App.jsx
import { Routes, Route, Link } from "react-router-dom";

export default function App() {
  return (
    <>
      <nav>
        <Link to="/">Inicio</Link>
        <Link to="/productos">Productos</Link>
        <Link to="/carrito">Carrito</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Inicio />} />
        <Route path="/productos" element={<Catalogo />} />
        <Route path="/carrito" element={<Carrito />} />
        <Route path="*" element={<NoEncontrado />} />
      </Routes>
    </>
  );
}
```

`Link` navega sin recargar. `NavLink` agrega una clase `active` a la ruta vigente.

## Navegación activa con `NavLink`

`NavLink` funciona como `Link` y permite estilizar el enlace de la ruta actual.

```jsx
import { NavLink } from "react-router-dom";

<NavLink
  to="/productos"
  className={({ isActive }) => (isActive ? "link activo" : "link")}
>
  Productos
</NavLink>
```
