# Práctica: API de Series con Express.js 📺

Esta práctica te guiará en la creación de un servidor HTTP usando **Express.js**, donde gestionarás un catálogo de series definido en un archivo JSON. Aprenderás a manejar rutas con parámetros y a capturar errores globalmente con `app.use`.

## 🎯 Objetivo

Construir un servidor Express que exponga un API REST para consultar información sobre series (películas o programas) desde un JSON estático, manejando rutas con parámetros y errores.

## 📂 Estructura de archivos

- `index.js` → punto de entrada del servidor.
- `data/shows.json` → JSON con la lista de series (formato descrito abajo).
- `package.json` → dependencias de Express y scripts.

El JSON (`data/shows.json`) tendrá un formato así:

```json
[
  {
    "id": 3,
    "poster": "./posters/3.jpg",
    "titulo": "The Mandalorian",
    "categoria": "Serie",
    "genero": "Sci-Fi, Fantasía, Acción",
    "gen": "Ciencia Ficción",
    "busqueda": "The Mandalorian, Sci-Fi, Fantasía, Suspenso, Pedro Pascal, ...",
    "resumen": "Ambientada tras la caída del Imperio y antes de la aparición...",
    "temporadas": 2,
    "reparto": "Pedro Pascal, Carl Weathers,...",
    "trailer": "https://www.youtube.com/embed/aOC8E8z_ifw"
  },
  {
    "id": 4,
    "poster": "./posters/4.jpg",
    "titulo": "The Umbrella Academy",
    "categoria": "Serie",
    "gen": "Ciencia Ficción",
    "genero": "Sci-Fi, Fantasía, Drama",
    "busqueda": "The Umbrella Academy, Sci-Fi, Fantasía, Drama, Tom Hopper,...",
    "resumen": "La muerte de su padre reúne a unos hermanos distanciados...",
    "temporadas": 1,
    "reparto": "Tom Hopper, David Castañeda,...",
    "trailer": "https://www.youtube.com/embed/KHucKOK-Vik"
  }
  // ... más objetos
]
```

## ✨ Características Implementadas

- Servidor HTTP con Express (`index.js`).
- Carga de datos desde `data/shows.json` usando `fs`.
- Rutas con parámetros para consultar todas las series, por ID, por título parcial, y para verificar existencia exacta.
- Manejo de errores: rutas no encontradas y errores de parámetros.
- Middleware global con `app.use` para errores 404 y 500.

## 🚦 Rutas Disponibles

1. `GET /` → Mensaje de bienvenida:
   ```json
   { "mensaje": "Bienvenido al catálogo de Series con Express!" }
   ```

2. `GET /shows/all` → Devuelve **todas** las series.

3. `GET /shows/id/:id` → Busca una serie por su `id`:
   - **200 OK** → Objeto serie.
   - **400 Bad Request** → Si `id` no es un número.
   - **404 Not Found** → Si no existe serie con ese `id`.

4. `GET /shows/titulo/:titulo` → Busca series cuyo `titulo` contenga el texto (parcial e insensible a mayúsculas):
   - **200 OK** → Array de series (puede venir vacío).

5. `GET /shows/existe/:titulo` → Verifica si existe **exactamente** una serie con ese `titulo` (insensible a mayúsculas):
   - **200 OK** → `{ "titulo": "...", "existe": true|false }`

6. Cualquier otra ruta → **404 Not Found** → `{ "error": "Ruta no encontrada" }`

## 🚀 Cómo arrancar

1. Clona el repositorio y accede al proyecto.
2. Instala dependencias:
   ```bash
   npm install express
   ```
3. Crea la carpeta `data` y agrega `shows.json` con el formato dado.
4. Ejecuta el servidor:
   ```bash
   node index.js
   ```
5. Proba las rutas con `curl`, Postman o REST Client (`.http`).

## 💡 Notas de Implementación

- Carga de JSON: `const shows = JSON.parse(fs.readFileSync('./data/shows.json'))`.
- Conversión de parámetros: chequea `isNaN(id)` antes de usar.
- Búsqueda parcial: usa `String.prototype.toLowerCase().includes()`.
- Existencia exacta: usa `Array.prototype.some()` con comparación en minúsculas.
- Captura errores 404 y 500 con middleware:
  ```js
  app.use((req, res) => {
    res.status(404).json({ error: 'Ruta no encontrada' })
  })

  app.use((err, req, res, next) => {
    console.error(err.stack)
    res.status(500).json({ error: 'Error interno del servidor' })
  })
  ```

## ✅ Ejemplos de respuestas

```bash
# GET /shows/all
[ { "id": 3, "titulo": "The Mandalorian", ... }, { "id": 4, "titulo": "The Umbrella Academy", ... } ]

# GET /shows/id/3
{ "id": 3, "titulo": "The Mandalorian", ... }

# GET /shows/id/abc
{ "error": "ID inválido" } (400 Bad Request)

# GET /shows/id/999
{ "error": "Serie con ID 999 no encontrada" } (404 Not Found)

# GET /shows/titulo/mandalorian
[ { "id": 3, "titulo": "The Mandalorian", ... } ]

# GET /shows/existe/The Umbrella Academy
{ "titulo": "the umbrella academy", "existe": true }
```
