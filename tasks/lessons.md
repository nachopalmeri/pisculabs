## 📚 Clase del Profesor — Refactorización Arquitectónica de HTML a Astro

### 🎯 Problema
Mantenías archivos monolíticos de HTML (`index.html` y `proyectos.html`) de cientos de líneas donde la estructura repetitiva (como el Navbar o Footer) estaba acoplada con los datos de cada tarjeta de proyecto. Si querías cambiar un link del menú, debías tocar todo. Además, la data del portfolio vivía inyectada en crudo dentro del "markup", dificultando su mantenibilidad a largo plazo.

### 🧠 Concepto Clave
**Componentización y Separación de Concerns (Data-Driven UI):** 
Desacoplamos completamente "qué mostramos" (Los Datos) de "cómo se ve" (La Interfaz). Utilizar Astro nos permite crear "Componentes tontos" (Dumb Components, como `ProjectCard.astro`) cuyo único propósito es recibir un objeto de datos y pintarlo, sin conocer la lógica de negocio.

### 🔬 La Solución
1. **Extracción UI**: Movimos la cáscara del sitio al componente fundamental `Layout.astro` y las barras de navegación a `Navbar.astro` y `Footer.astro`.
2. **Data-Driven (JSON)**: Se extrajo toda la información cruda de proyectos hacia `src/data/proyectos.json`. 
3. **Array Mapping en lugar de repetición**:
```astro
// src/pages/index.astro
// Iteramos el JSON y construimos dinámicamente cada tarjeta
{proyectosData.map((proj) => (
  <ProjectCard id={proj.id} desc={proj.previewDesc} {...proj} />
))}
```

### ⚖️ Trade-offs
- ✅ **Ganamos**: Extrema mantenibilidad. Si deseas mutar un dato o agregar un proyecto, solo tocas el archivo `proyectos.json`. No tocas HTML.
- ✅ **Ganamos**: Reducción drástica del tamaño de código duplicado.
- ⚠️ **Sacrificamos**: Añadimos una capa de abstracción técnica. Ahora hay que entender qué es un *Array.map* para entender cómo se dibujan las tarjetas.

### 🧪 Verificación
```bash
npm run dev
```
Output esperado: La aplicación levanta en `http://localhost:4321` renderizando de la misma forma idéntica sin archivos `.html` crudos y leyendo fielmente el JSON. Además, revisando en tu navegador, notarás que no hay CSS ni JS que falte.

### 💡 Lección
> "El mejor código es aquel que no necesita reescribirse cuando cambian los datos, sino que simplemente fluye adaptándose a la nueva información entrante."

---

## 📚 Clase del Profesor — El Regreso del Neo-Techno y la "Delightful" UI

### 🎯 Problema
En nuestro intento de simplificar ("Destilar"), caímos en la trampa del Brutalismo extremo. El minimalismo riguroso y los fondos blancos, aunque académicos y performantes, **le robaron el alma al proyecto** matando el estilo originario (oscuro, cinemático) y eliminando interacciones amadas como el hover holográfico y la reactividad del cursor. La web quedó aburrida.

### 🧠 Concepto Clave
**UI Delight y Diseño Bolder (Neo-Cyberpunk):**
A veces, un usuario o marca pide *ruido*, energía e interacción tecnológica en lugar de páginas de diario (periódico). En lugar de aplicar "AI Slop" genérico, aplicamos diseño *Bolder*:
1. **Glassmorphism controlado**: No difuminamos todo al azar. Creamos contenedores (`border-radius: 8px; background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(10px)`) para un look premium oscuro.
2. **Animaciones Fluidas (Physics)**: Introdujimos curvas pseudo-físicas (`cubic-bezier` tipo **Spring**) para que cuando hagas hover en un proyecto, la tarjeta rebote y la flecha salte, sintiéndose viva y no "programada estáticamente".
3. **El Ratón como Luz**: Reinventamos tu viejo "glow effects" inyectando `<div class="mouse-light">` guiado milimétricamente por JavaScript para crear un efecto de "linterna" sobre la grilla (`background-image`) del documento. 

### 🔬 La Solución
- **Reescribimos CSS y Color**: Se abandonó OKLCH claro para establecer `#030303` profundo. Restablecimos el resplandor púrpura `rgba(139, 92, 246, 0.4)` solo en acciones enfocadas y no como un moco perenne interrumpiendo lectura.
- **Tipografía Agresiva**: Reintroducida una fuente monoespaciada dura (`DM Mono`) para metadatos, haciendo los botones y badges sentirse como terminales de mando frente a grandes encabezados en `Inter`.
- **Efecto de Glitch Controlado**: En el CTA "realidad" añadimos un `text-stroke` con un seudo-elemento superpuesto que pulsa continuamente para atrapar miradas (Ruta Neo-techno).

### ⚖️ Trade-offs
- ✅ **Ganamos**: Una identidad personal inolvidable, hiper-interactiva que saca una "sonrisa" (`Delight UI`) a quien cliquea y muestra maestría técnica en CSS/JS.
- ✅ **Ganamos**: Le devolvimos la marca a su creador (tu estilo está vivo, pero ejecutado profesionalmente).
- ⚠️ **Sacrificamos**: Unos milisegundos de performance en móviles muy viejos por la luz constante del mouse (aunque forzamos `passive: true` para mitigar bloqueos del main thread) y por pintar tantos filtros de desenfoque (`blur`). 

### 🧪 Verificación
Ejecuta la preview:
```bash
npm run dev
```
Ve a `localhost:4322`, mueve tu ratón. Sentí el salto elástico al pasar sobre las "Pain cards". Este no es "un sitio más"; es una pieza de software.

### 💡 Lección
> "La experiencia de usuario no es solo utilidad fría. A veces, la fricción calculada, las sombras profundas y la luz persiguiendo tu cursor son, precisamente, el valor necesario para evocar asombro."

---

## 📚 Clase del Profesor — 3D Parallax & WebGL Interactivity

### 🎯 Problema
Las interacciones en el portfolio eran fluidas pero planas (2D). Las tarjetas de proyecto reaccionaban a la rotación (`tilt`) pero sus títulos e imágenes seguían pegados al fondo. Además, los menús móviles aparecían con una transición brusca (`display: none` a `flex`) restando calidad a la experiencia global, y el grid de fondo CSS se sentía repetitivo.

### 🧠 Concepto Clave
**Percepción de Profundidad (Parallax) y Física Ui:**
Al aplicar `transform: translateZ(px)` a los hijos de un contenedor que tiene movimiento 3D (`transform-style: preserve-3d` con una rotación pseudo-física en hover), los objetos de la capa delantera se cruzan virtualmente con el plano de visión, dándonos un parallax puramente nativo mediante CSS.
Para animaciones más performantes y profundas que el DOM no puede manejar fluidamente en masa, utilizamos la `Canvas API` (WebGL/2D Context), creando nodos que calculan distancias euclidianas para unir puntos ("Constelación") y reaccionar al cursor sin penalizar al "Main Thread" en el pintado de componentes Astro.

### 🔬 La Solución
1. **Profundidad Cúbica (Z-Axis):** Añadimos un translateZ a `.proj-card-content` y `.proj-card-img` en estado *:hover* del contenedor.
2. **Spring Menu:** Cambiamos el toggle de estado de `.m-nav` de `display: none` a un manejo de *opacity* y *transform: translateY*, permitiendo que la transición interpolada sea procesada por el compositor de la GPU sin repintar el Layout.
3. **Escena Interactiva:** Reemplazamos la cuadrícula `body::before` con un script incrustado en `Layout.astro` que inicializa partículas dinámicas conectadas por líneas sensibles al gradiente `hsla/rgba` de nuestro tema (Violeta y Verde).

### ⚖️ Trade-offs
- ✅ **Ganamos:** Un efecto hipnótico, moderno y espacial que grita "Engineering Excellence". Retuvimos el feedback háptico-visual en las tarjetas.
- ⚠️ **Sacrificamos:** Consumo de batería. Un `<canvas>` calculando la hipotenusa matemática de cientos de puntos en cada `requestAnimationFrame` a 60 FPS cuesta energía. (Por eso implementamos un tope responsivo a las partículas dependiendo del `window.innerWidth`).

### 🧪 Verificación
```bash
npm run dev
```
Haz hover en un Proyecto y observa cómo la imagen se asoma de la pantalla. Haz clic en el botón de hamburguesa en modo móvil y observa el menú colgar suavemente.

### 💡 Lección
> "La verdadera inmersión web rara vez ocurre dibujando cajas 2D estáticas. El deleite real emerge cuando inyectamos un eje Z y hacemos que las matemáticas respondan al movimiento de la mano del usuario."
