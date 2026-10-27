# Tema 13. Formularios controlados

El carrito global del tema 12 ya permite llegar a la etapa final de la compra: el checkout. Este tema trabaja con formularios controlados y validación en React para capturar los datos del comprador. En el tema 14 se ordena el trabajo en equipo con Git y en el 15 se publica la aplicación.

## Componentes controlados

El estado de React es la única fuente de verdad del valor de cada campo.

```jsx
function Checkout() {
  const [form, setForm] = useState({ nombre: "", email: "", direccion: "" });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setForm((prev) => ({ ...prev, [name]: value }));
  };

  return (
    <form>
      <input name="nombre" value={form.nombre} onChange={handleChange} />
      <input name="email" type="email" value={form.email} onChange={handleChange} />
      <input name="direccion" value={form.direccion} onChange={handleChange} />
    </form>
  );
}
```

Un único `handleChange` con `name` evita escribir un manejador por campo.

## Reglas de validación

Se definen como una función pura, fácil de probar y reutilizar.

```jsx
function validar({ nombre, email, direccion }) {
  const errores = {};

  if (nombre.trim().length < 3) {
    errores.nombre = "El nombre debe tener al menos 3 caracteres";
  }
  if (!/^\S+@\S+\.\S+$/.test(email)) {
    errores.email = "Ingresá un email válido";
  }
  if (!direccion.trim()) {
    errores.direccion = "La dirección es obligatoria";
  }
  return errores;
}
```

## Mostrar errores y controlar el envío

Los errores se muestran solo en campos que el usuario ya visitó (`touched`), para no mostrar el formulario "en rojo" desde el inicio.

```jsx
const [errores, setErrores] = useState({});
const [touched, setTouched] = useState({});
const [enviando, setEnviando] = useState(false);
const { items, total, vaciar } = useCart();

const handleBlur = (e) => {
  setTouched((prev) => ({ ...prev, [e.target.name]: true }));
  setErrores(validar(form));
};

const handleSubmit = async (e) => {
  e.preventDefault();
  const erroresActuales = validar(form);
  setErrores(erroresActuales);
  setTouched({ nombre: true, email: true, direccion: true });

  if (Object.keys(erroresActuales).length > 0) return;

  try {
    setEnviando(true);
    await api.post("/orders", { comprador: form, items, total });
    vaciar();
    navigate("/gracias");
  } finally {
    setEnviando(false);
  }
};
```

```jsx
<form onSubmit={handleSubmit} noValidate>
  <label>
    Email
    <input name="email" value={form.email} onChange={handleChange} onBlur={handleBlur} />
    {touched.email && errores.email && <span role="alert">{errores.email}</span>}
  </label>

  <button type="submit" disabled={enviando || items.length === 0}>
    {enviando ? "Procesando..." : "Confirmar compra"}
  </button>
</form>
```

## Otros tipos de campo

```jsx
<select name="envio" value={form.envio} onChange={handleChange}>
  <option value="estandar">Estándar</option>
  <option value="express">Express</option>
</select>

<input
  type="checkbox"
  name="terminos"
  checked={form.terminos}
  onChange={(e) => setForm({ ...form, terminos: e.target.checked })}
/>
```

## Componentes no controlados

Para formularios simples se puede leer el DOM al enviar, sin estado por campo:

```jsx
const handleSubmit = (e) => {
  e.preventDefault();
  const datos = Object.fromEntries(new FormData(e.target));
};
```

## Buenas prácticas

- Validar en el cliente mejora la experiencia, pero **siempre** también en el servidor.
- Asociar cada `input` a su `label` y comunicar errores con `role="alert"`.
- Para formularios grandes considerar librerías como React Hook Form con Zod.
