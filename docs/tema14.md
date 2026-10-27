# Tema 14. Gitflow y tableros Kanban

La tienda ya está completa a nivel funcional. Antes de publicarla (tema 15), este tema presenta cómo organizar el trabajo en equipo: un modelo de ramas (Gitflow) y un tablero de tareas (Kanban). Ambos se aplican sobre el repositorio del proyecto.

## Gitflow

Modelo de ramas con roles definidos.

| Rama | Propósito | Se crea desde | Se integra en |
|------|-----------|---------------|---------------|
| `main` | Código en producción | — | — |
| `develop` | Integración de funcionalidades | `main` | `main` (al liberar) |
| `feature/*` | Una funcionalidad | `develop` | `develop` |
| `release/*` | Preparación de una versión | `develop` | `main` y `develop` |
| `hotfix/*` | Corrección urgente en producción | `main` | `main` y `develop` |

## Inicializar el repositorio

```bash
git init
git add .
git commit -m "chore: proyecto inicial de la tienda"
git branch -M main
git remote add origin https://github.com/usuario/tienda.git
git push -u origin main
git switch -c develop
git push -u origin develop
```

## Ciclo de una funcionalidad

Ejemplo: agregar el filtro por categoría.

```bash
git switch develop
git pull
git switch -c feature/filtro-categorias

# ...desarrollo...
git add .
git commit -m "feat: filtro de productos por categoría"
git push -u origin feature/filtro-categorias
```

Luego se abre un **Pull Request** hacia `develop`, se revisa y se fusiona. Finalmente se elimina la rama:

```bash
git switch develop
git pull
git branch -d feature/filtro-categorias
```

## Liberar una versión

```bash
git switch -c release/1.0.0 develop
# ajustes finales: versión, documentación
git switch main
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Primera versión de la tienda"
git switch develop
git merge --no-ff release/1.0.0
git push origin main develop --tags
```

## Hotfix

```bash
git switch -c hotfix/total-carrito main
git commit -am "fix: corrige cálculo del total con descuento"
git switch main && git merge --no-ff hotfix/total-carrito
git switch develop && git merge --no-ff hotfix/total-carrito
```

## Convención de commits

Formato `tipo: descripción` (Conventional Commits).

| Tipo | Uso |
|------|-----|
| `feat` | Nueva funcionalidad |
| `fix` | Corrección de error |
| `docs` | Documentación |
| `refactor` | Cambio interno sin alterar comportamiento |
| `chore` | Tareas de mantenimiento |

## Resolución de conflictos

Ocurren cuando dos ramas modifican las mismas líneas. Git marca el archivo:

```text
<<<<<<< HEAD
const envioGratis = 200;
=======
const envioGratis = 250;
>>>>>>> feature/envios
```

Se edita el archivo dejando la versión correcta, se elimina cada marcador, y luego:

```bash
git add archivo.js
git commit
```

## Kanban

Método visual para gestionar el flujo de trabajo mediante un tablero con columnas. En GitHub se usa **Projects**.

| Por hacer | En curso | En revisión | Hecho |
|-----------|----------|-------------|-------|
| Checkout | Filtro de categorías | Carrito con contexto | Catálogo |
| Página de gracias | | | Detalle de producto |

Principios:

- Cada tarjeta representa una tarea pequeña y concreta (una `feature`).
- Limitar el trabajo en curso: pocas tarjetas simultáneas por persona.
- Vincular cada tarjeta con su rama y su Pull Request (`Closes #12` en la descripción).
