# Tema 7. Configuración de React

Desde este tema la tienda se reconstruye con React. Se prepara el entorno de trabajo: creación del proyecto con Vite, estructura de carpetas y dependencias que se usan en el resto de la materia. Es el paso previo al tema 8, donde se escriben los primeros componentes.

## Crear el proyecto

```bash
npm create vite@latest tienda -- --template react
cd tienda
npm install
npm run dev
```

## Estructura

```text
src/
├── components/
│   ├── ProductCard.jsx
│   └── ProductList.jsx
├── App.jsx
└── main.jsx
```

## Archivos principales

`index.html` contiene el punto de montaje y `src/main.jsx` renderiza la aplicación en él.

```jsx
// src/main.jsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

```jsx
// src/App.jsx
export default function App() {
  return <h1>TechShop</h1>;
}
```

`StrictMode` ejecuta algunas comprobaciones extra en desarrollo (por ejemplo, ejecuta los efectos dos veces) para detectar errores temprano.

## Dependencias de la materia

```bash
npm install axios react-router-dom
```

| Paquete | Se usa en |
|---------|-----------|
| `axios` | Tema 9 |
| `react-router-dom` | Temas 10 y 11 |

## Scripts

| Comando | Función |
|---------|---------|
| `npm run dev` | Servidor de desarrollo con recarga automática |
| `npm run build` | Compilación para producción en `dist/` |
| `npm run preview` | Prueba local del build |
| `npm run lint` | Análisis estático con ESLint |
