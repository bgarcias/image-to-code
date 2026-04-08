---
name: image-to-code
description: Convierte imágenes de diseño (Figma exports, mockups, wireframes, screenshots) en código HTML/CSS limpio, semántico, accesible y con buenas prácticas de SEO. Usar cuando el usuario proporcione una imagen de una página o componente y pida convertirla a código. También aplica cuando el usuario diga "convierte esta imagen", "genera el código de esta pantalla", "crea el HTML de este diseño" o similar.
---

# Image to Code

Convierte imágenes de diseño en código HTML/CSS semántico, estructurado, accesible y listo para desarrollar.

Esta skill es agnóstica al stack — funciona para landing pages, sitios estáticos, componentes aislados, o cualquier proyecto web. No asume frameworks, no asume CMS.

---

## Cuándo activar esta skill

- El usuario proporciona una imagen (JPG, PNG, WebP) de un diseño, mockup, wireframe o screenshot
- El usuario pide convertir, replicar, o generar código a partir de una imagen visual
- El usuario menciona Figma, Photoshop, Useritor u otra herramienta de diseño como fuente
- El usuario dice "convierte", "replica", "genera el HTML/CSS de esto"

---

## Paso 1 — Analizar la imagen

Antes de escribir una sola línea de código, examinar la imagen en detalle:

**Estructura:**
- Layout general (contenedor, columnas, filas)
- Secciones principales (hero, nav, grid, cards, footer, etc.)
- Jerarquía visual (qué es contenedor, qué es hijo)

**Componentes:**
- Navegación (horizontal, hamburger, mega menú, dropdown)
- Botones (forma, tamaño, variantes visibles)
- Cards (estructura interna: imagen, texto, CTA)
- Formularios (campos, labels, submit)
- Imágenes (dónde van, proporciones aproximadas)
- Iconos (si los hay, marcar como placeholder)

**Estilo visual:**
- Colores predominantes (extraer hex aproximado si es visible)
- Bordes redondeados (none / leve / medio / alto)
- Sombras (si las hay y son claramente visibles)
- Espaciado general (compacto / normal / generoso)
- Tipografía aparente (tamaños relativos entre heading y body)

---

## Paso 2 — Confirmar output antes de generar

Si el usuario no especificó, preguntar UNA SOLA VEZ:

1. **¿Es una página completa o un componente aislado?**
2. **¿Tiene prefijo CSS definido para el proyecto?** (ej: `ph-`, `lp-`, `site-`) — si no, usar el nombre del proyecto en kebab-case
3. **¿Tiene variables CSS ya definidas?** — si no, la skill las genera a partir de los colores de la imagen

Si hay un `CLAUDE.md` en el proyecto con convenciones definidas, leerlo antes de preguntar.

---

## Paso 3 — Generar el código

### Reglas obligatorias — HTML

- Elementos semánticos siempre: `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>`
- Cada sección lleva un comentario descriptivo: `<!-- SECCIÓN: Hero -->`
- Clases en kebab-case con prefijo del proyecto: `ph-hero`, `ph-product-card`
- No usar IDs para estilos, solo clases
- Un solo `<h1>` por página — jerarquía de headings sin saltar niveles (h1 → h2 → h3)
- Imágenes sin asset real → placeholder visual (ver sección de placeholders)

### Reglas obligatorias — CSS

**Variables globales en `:root`:**
```css
:root {
  /* Colores — extraídos de la imagen o definidos por el proyecto */
  --color-primary:   #000000; /* reemplazar con valor real */
  --color-secondary: #000000;
  --color-text:      #000000;
  --color-bg:        #ffffff;
  --color-bg-alt:    #f5f5f5;

  /* Tipografía */
  --font-heading: sans-serif; /* reemplazar con fuente del proyecto */
  --font-body:    sans-serif;

  /* Espaciado base */
  --spacing-xs:  0.5rem;
  --spacing-sm:  1rem;
  --spacing-md:  1.5rem;
  --spacing-lg:  2.5rem;
  --spacing-xl:  4rem;
}
```

Variables locales solo para valores muy específicos de una sección:
```css
/* Correcto — variable local para algo único de esa sección */
.ph-hero {
  --hero-min-height: 600px;
}
```

**Desktop-first obligatorio:**
- Estilos base escritos para pantallas grandes
- Media queries siempre con `max-width`, nunca `min-width` como punto de partida

```css
/* Breakpoints de referencia */
@media (max-width: 1024px) { } /* Tablet  */
@media (max-width: 768px)  { } /* Mobile  */
@media (max-width: 480px)  { } /* Mobile S*/
```

**Otras reglas CSS:**
- Layouts con `flexbox` o `CSS grid` según el diseño
- Unidades: `rem` para tipografía, `px` para bordes/sombras, `%` o `fr` para layouts fluidos
- No usar `!important` salvo caso extremo con comentario explicativo
- No inventar hover states ni animaciones que no estén en la imagen — dejar `/* TODO: hover state */`

### Placeholders para imágenes sin asset

```html
<!-- Placeholder: [descripción de qué imagen va aquí] -->
<div class="ph-hero__image img-placeholder" role="img" aria-label="[descripción de la imagen]">
  <!-- Reemplazar con <img src="..." alt="..."> cuando el asset esté disponible -->
</div>
```

```css
.img-placeholder {
  background-color: #D9D9D9;
  min-height: 320px; /* ajustar según proporciones del diseño */
  border-radius: inherit;
  display: flex;
  align-items: center;
  justify-content: center;
}

.img-placeholder::after {
  content: '[imagen]';
  color: #999;
  font-size: 0.875rem;
  font-family: sans-serif;
}
```

Si el diseño indica un color de fondo específico para la imagen (ej: imagen lifestyle sobre fondo azul), usar ese color en lugar del gris neutro `#D9D9D9`.

### Carruseles

Cualquier carrusel detectado en la imagen se implementa **siempre con Embla Carousel** — nunca con código custom.

- Cargar vía CDN: `https://unpkg.com/embla-carousel/embla-carousel.umd.js`
- Inicialización mínima en un `<script>` al final del `<body>`
- Estructura HTML obligatoria:
```html
  <div class="[prefix]-carousel embla">
    <div class="embla__container">
      <div class="embla__slide"><!-- item --></div>
      <div class="embla__slide"><!-- item --></div>
    </div>
  </div>
```
- No inventar controles (prev/next/dots) que no estén visibles en el diseño — dejar `<!-- TODO: controles -->`

---

## Paso 4 — SEO y accesibilidad

Aplicar siempre, en cada output, sin excepción:

### SEO estructural

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- SEO básico — completar con contenido real -->
  <title><!-- Título de la página | Nombre del sitio --></title>
  <meta name="description" content="<!-- Descripción de 150-160 caracteres -->">
  <link rel="canonical" href="<!-- URL canónica -->">

  <!-- Open Graph — para compartir en redes -->
  <meta property="og:title" content="<!-- Título -->">
  <meta property="og:description" content="<!-- Descripción -->">
  <meta property="og:image" content="<!-- URL de imagen OG, 1200x630px -->">
  <meta property="og:type" content="website">
  <meta property="og:url" content="<!-- URL -->">
</head>
```

- Un solo `<h1>` por página
- Headings en orden jerárquico (nunca saltar de h1 a h3)
- Links descriptivos — nunca texto "clic aquí" o "ver más" sin contexto
- URLs limpias y descriptivas en los `href`

### Accesibilidad

**Imágenes:**
```html
<!-- Imagen con contenido — alt descriptivo -->
<img src="producto.jpg" alt="Arthron — suplemento para articulaciones, frasco de 60 tabletas">

<!-- Imagen decorativa — alt vacío para que el lector de pantalla la ignore -->
<img src="decoracion.svg" alt="">
```

**Botones e interactivos:**
```html
<!-- Botón con texto visible — no necesita aria-label -->
<button type="button">Ver producto</button>

<!-- Botón solo con ícono — necesita aria-label -->
<button type="button" aria-label="Cerrar menú">
  <svg>...</svg>
</button>
```

**Navegación:**
```html
<nav aria-label="Navegación principal">
  ...
</nav>
```

**Formularios:**
```html
<!-- Label siempre asociado al input -->
<label for="nombre">Nombre completo</label>
<input type="text" id="nombre" name="nombre" required>
```

**Contraste y foco:**
- No eliminar el outline de foco (`outline: none` sin reemplazo está prohibido)
- Si se quita el outline nativo, reemplazarlo con estilo personalizado visible:
```css
:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 3px;
}
```

---

## Paso 5 — Estructura de entrega

### Página completa:
```
index.html        ← o el nombre de la página
styles/
  main.css        ← variables globales + reset + estilos base
  [pagina].css    ← estilos específicos de esa página
```

### Componente aislado:
```
[componente].html  ← solo el fragmento HTML del componente, sin <html>/<body>
[componente].css   ← estilos del componente con su `:root` local si hace falta
```

---

## Paso 6 — Notas al entregar

Siempre incluir al final:

```
## Notas de implementación

- Placeholders: [qué imágenes son placeholder y dónde reemplazarlas]
- Variables CSS: [qué variables se crearon y sus valores aproximados]
- SEO pendiente: [qué campos de meta/OG hay que completar con contenido real]
- Accesibilidad: [cualquier decisión tomada o elemento que requiere revisión]
- Pendientes: [hover states, JS, assets faltantes, decisiones ambiguas del diseño]
```

---

## Lo que esta skill NO hace

- No genera JavaScript de interacción custom — la única excepción es la inicialización de Embla Carousel para carruseles detectados en el diseño
- No usa frameworks CSS (Tailwind, Bootstrap) salvo que el usuario lo pida explícitamente
- No inventa secciones que no estén en la imagen
- No aplica mobile-first bajo ninguna circunstancia
- No duplica variables CSS en cada página — siempre `:root` global, variables locales solo para casos específicos
