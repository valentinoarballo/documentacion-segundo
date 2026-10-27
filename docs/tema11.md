# Tema 11. Rutas dinámicas y anidadas

Se completa el enrutamiento de la tienda con rutas dinámicas (el detalle de un producto según su id) y rutas anidadas (layouts compartidos). Continúa el tema 10; en el tema 12 se resuelve cómo compartir el carrito entre todas las páginas.

## Rutas dinámicas

Un segmento con `:` es un parámetro, que se lee con `useParams`.

```jsx
<Route path="/productos/:id" element={<DetalleProducto />} />
```

```jsx
import { useParams } from "react-router-dom";

function DetalleProducto() {
  const { id } = useParams();
  const [producto, setProducto] = useState(null);

  useEffect(() => {
    api.get(`/products/${id}`).then((res) => setProducto(res.data));
  }, [id]);

  if (!producto) return <p>Cargando...</p>;
  return <h1>{producto.title}</h1>;
}
```

En la tarjeta del catálogo se enlaza al detalle:

```jsx
<Link to={`/productos/${producto.id}`}>Ver detalle</Link>
```

## Rutas anidadas

Un layout compartido se define con `Outlet`, que indica dónde se dibuja la ruta hija.

```jsx
import { Outlet } from "react-router-dom";

function Layout() {
  return (
    <>
      <Header />
      <main><Outlet /></main>
      <Footer />
    </>
  );
}
```

```jsx
<Routes>
  <Route element={<Layout />}>
    <Route index element={<Inicio />} />
    <Route path="productos" element={<Catalogo />} />
    <Route path="productos/:id" element={<DetalleProducto />} />
    <Route path="carrito" element={<Carrito />} />
  </Route>
</Routes>
```

Anidamiento por sección (panel de cuenta):

```jsx
<Route path="cuenta" element={<CuentaLayout />}>
  <Route index element={<Perfil />} />
  <Route path="pedidos" element={<Pedidos />} />
</Route>
```

## Navegación programática y parámetros de consulta

```jsx
import { useNavigate, useSearchParams } from "react-router-dom";

const navigate = useNavigate();
navigate("/carrito");

const [params, setParams] = useSearchParams();
const categoria = params.get("categoria"); // /productos?categoria=laptops
setParams({ categoria: "smartphones" });
```
