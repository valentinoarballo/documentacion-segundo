# Tema 15. Formulario de carga y despliegue en Vercel

Se agrega a la tienda un formulario para cargar productos que envía los datos a la API, y luego se publica la aplicación en Vercel. Combina los formularios controlados del tema 13 con `axios` (tema 9) y el flujo de ramas del tema 14. La configuración de la conexión con la API en producción se detalla en el tema 16.

## Formulario de carga de productos

Un formulario controlado (tema 13) que envía los datos del producto a la API con `axios` (tema 9).

```jsx
// src/components/ProductForm.jsx
import { useState } from "react";
import { api } from "../api/client";

const inicial = { nombre: "", precio: "", stock: "" };

export default function ProductForm({ onCreado }) {
  const [form, setForm] = useState(inicial);
  const [error, setError] = useState(null);
  const [enviando, setEnviando] = useState(false);

  const handleChange = (e) =>
    setForm((prev) => ({ ...prev, [e.target.name]: e.target.value }));

  const handleSubmit = async (e) => {
    e.preventDefault();

    if (!form.nombre.trim() || Number(form.precio) <= 0) {
      setError("Completá el nombre y un precio mayor a 0");
      return;
    }

    try {
      setEnviando(true);
      setError(null);
      const { data } = await api.post("/productos", {
        nombre: form.nombre.trim(),
        precio: Number(form.precio),
        stock: Number(form.stock) || 0,
      });
      onCreado?.(data);
      setForm(inicial);
    } catch (err) {
      setError(err.message);
    } finally {
      setEnviando(false);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="nombre" placeholder="Nombre" value={form.nombre} onChange={handleChange} />
      <input name="precio" type="number" placeholder="Precio" value={form.precio} onChange={handleChange} />
      <input name="stock" type="number" placeholder="Stock" value={form.stock} onChange={handleChange} />
      {error && <p role="alert">{error}</p>}
      <button type="submit" disabled={enviando}>
        {enviando ? "Guardando..." : "Agregar producto"}
      </button>
    </form>
  );
}
```

El componente padre actualiza el listado con el producto creado, sin volver a pedir todo el catálogo:

```jsx
<ProductForm onCreado={(nuevo) => setProductos((prev) => [...prev, nuevo])} />
```

## Compilar para producción

```bash
npm run build     # genera la carpeta dist/
npm run preview   # prueba local del build
```

## Rutas del lado del cliente

Con React Router, recargar `/productos/3` en un hosting estático devuelve 404 porque ese archivo no existe. Se resuelve redirigiendo todas las rutas a `index.html`.

En Vercel (`vercel.json`):

```json
{ "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }] }
```

En Netlify (`public/_redirects`):

```text
/*  /index.html  200
```

## Publicar

1. Subir el proyecto a GitHub (rama `main`).
2. En Vercel o Netlify, importar el repositorio.
3. Configurar: build command `npm run build`, directorio de salida `dist`.
4. Definir `VITE_API_URL` en las variables de entorno del servicio.
5. Desplegar. Cada push a `main` genera un nuevo despliegue.
