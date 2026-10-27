# Tema 16. Configuración de conexión en aplicaciones desplegadas

Una aplicación desplegada debe comunicarse con una API que también está en línea. Este tema cubre las variables de entorno, CORS y el manejo uniforme de errores para que la tienda funcione en producción igual que en desarrollo. Completa el tema 15 y cierra la materia.

## Variables de entorno

La URL de la API cambia entre desarrollo y producción, por lo que no debe escribirse en el código. En Vite las variables expuestas al cliente deben empezar con `VITE_`.

```bash
# .env.development
VITE_API_URL=http://127.0.0.1:8000

# .env.production
VITE_API_URL=https://mi-api.onrender.com
```

```jsx
// src/api/client.js
import axios from "axios";

export const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  timeout: 10000,
});
```

Los archivos `.env*` con secretos no se suben al repositorio:

```text
# .gitignore
.env
.env.local
```

Todo lo que lleva el prefijo `VITE_` queda visible en el navegador: nunca colocar claves privadas.

## CORS

Si el navegador bloquea las peticiones con un error de CORS, la API debe autorizar el origen del frontend. Ejemplo en FastAPI:

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:5173", "https://mi-tienda.vercel.app"],
    allow_methods=["*"],
    allow_headers=["*"],
)
```

## Consumir la API hosteada

```jsx
export const getArticulos = () => api.get("/articulos").then((r) => r.data);
export const crearArticulo = (data) => api.post("/articulos", data).then((r) => r.data);
```

Es conveniente tratar los errores de forma uniforme con un interceptor:

```jsx
api.interceptors.response.use(
  (res) => res,
  (error) => {
    const mensaje = error.response?.data?.detail ?? "No se pudo conectar con el servidor";
    return Promise.reject(new Error(mensaje));
  }
);
```

## Errores frecuentes en producción

| Síntoma | Causa probable | Solución |
|---------|----------------|----------|
| Error de CORS en consola | La API no autoriza el dominio del frontend | Agregar el dominio publicado a `allow_origins` |
| Las peticiones van a `localhost` | `VITE_API_URL` no definida en el servicio | Definirla y volver a desplegar |
| La variable cambió y no se refleja | Vite incrusta las variables al compilar | Redesplegar tras modificarla |
| Error de contenido mixto | Frontend en `https` consumiendo API en `http` | Usar la URL `https` de la API |
| 404 al recargar `/productos/3` | Falta la reescritura a `index.html` | Configurar `rewrites` (tema 15) |
| Primera respuesta muy lenta | La API gratuita se suspende por inactividad | Mostrar estado de carga y reintentar |

## Lista de verificación

- La aplicación carga productos desde la API de producción.
- Las rutas internas funcionan al recargar la página.
- No hay URLs `localhost` ni secretos en el código.
- La consola del navegador no muestra errores de CORS ni de red.
