# Manejo EventosM6L4
# Huerto El Alba - Centro de Eventos

¡Explora, haz clic, escribe y descubre cómo fluyen los eventos en Vue! 🌿

Aplicación educativa desarrollada con **Vue 3 Composition API** que demuestra el manejo de eventos DOM, modificadores de eventos y teclado, y la propagación de eventos (captura vs burbujeo). Temática de agricultura orgánica.

##  Funcionalidades destacadas

- **Formulario de búsqueda**: uso de `@keyup.enter`, `@keydown.esc` y `@submit.prevent`.
- **Tarjeta única interactiva**: demuestra `@click.capture` en el contenedor y `@click.stop` en el botón "Favorito".
- **Panel "Etapas de eventos"**: siete tarjetas que ejemplifican `@click.normal`, `.stop`, `.capture`, `.once`, `.prevent`, teclas (`enter`/`esc`) y `.passive` en scroll.
- **Botón de acción única**: `@click.once` para mostrar un tip agrícola que solo funciona una vez.
- **Caja scrollable**: con `@scroll.passive` que muestra la posición en tiempo real.
- **Enlace sin navegación**: `@click.prevent` que abre un modal informativo.
- **Registro de eventos (audit log)**: lista en tiempo real los últimos 30 eventos, con botón para limpiar.

## 🛠️ Tecnologías

- Vue 3 (Composition API)
- Vite
- HTML5 / CSS3 (diseño responsive, degradados y estilos mínimos)

## 📁 Estructura del proyecto
src/
├── App.vue # Componente principal
├── main.js # Punto de entrada
├── style.css # Estilos globales
└── components/
├── Tarjeta.vue # Tarjeta reutilizable con evento .stop
└── Modal.vue # Modal informativo

## ▶ GitHub
      https://github.com/NelDurv/Manejo-de-Eventos-Vue

     https://github.com/NelDurv/Manejo-de-Eventos-Vue/commit/32123fd1ffd89d485b2810b4d9145426cd91e3b9


## ▶️ Ejecución local

```bash
npm install
npm run dev

🎯 Objetivo educativo 
Demostrar dominio de:

Eventos DOM en Vue (en línea y mediante métodos).

Modificadores: .prevent, .stop, .once, .capture, .passive.

Modificadores de teclas: .enter, .esc, combinación .ctrl.k.




