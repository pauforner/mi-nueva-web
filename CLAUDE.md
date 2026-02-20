# MetaDigitales — Documentación del Proyecto

Landing page estática para **MetaDigitales**, comunidad digital donde se transforman ideas en negocios digitales automatizados con IA.

---

## Stack técnico

| Capa | Tecnología | Razón |
|---|---|---|
| Framework | Astro 5+ | Genera HTML estático puro, cero JS por defecto |
| CSS | Tailwind CSS v4 | Integración moderna vía `@tailwindcss/vite` |
| Lenguaje | TypeScript (strict) | Seguridad de tipos en componentes Astro |
| Fuentes | Google Fonts CDN | Carga ligera con `preconnect` en el layout |
| Deploy A | GitHub Pages | Vía GitHub Actions con `withastro/action@v3` |
| Deploy B | Vercel | Auto-detección de Astro, cero configuración |

---

## Decisiones de arquitectura

### Tailwind CSS v4 — integración correcta
Tailwind v4 **no usa** `@astrojs/tailwind` (deprecated). La integración correcta es:

```js
// astro.config.mjs
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  vite: { plugins: [tailwindcss()] },
});
```

```css
/* global.css — los @import de fuentes van ANTES de tailwindcss */
@import url('https://fonts.googleapis.com/...');
@import "tailwindcss";
```

> **Importante:** El `@import url(Google Fonts)` debe ir **antes** de `@import "tailwindcss"`, o Tailwind genera un warning en build.

### Design tokens en `@theme {}`
En Tailwind v4, el fichero `tailwind.config.js` desaparece. Todos los tokens van en `global.css`:

```css
@theme {
  --color-void: #06060f;
  --color-cyan: #00e5ff;
  --font-display: 'Manrope', system-ui, sans-serif;
}
```

Estos tokens generan automáticamente utilidades como `bg-void`, `text-cyan`, `font-display`.

### Sin path aliases
Se decidió usar imports relativos (`../components/Header.astro`) en lugar de aliases `@components/*` para evitar problemas de resolución entre TypeScript y Vite en proyectos pequeños.

### Cero JS en cliente (por defecto)
Astro no hidrata nada salvo lo que lleve `client:*`. El único JS presente es:
- `Header.astro`: scroll effect + toggle menú móvil (nativo, sin framework)
- Hero: terminal animada con `@keyframes` CSS puro

---

## Design System

### Paleta de colores

| Token | Hex | Uso |
|---|---|---|
| `--color-void` | `#06060f` | Fondo base (negro espacial) |
| `--color-surface` | `#0b0b1a` | Secciones alternadas |
| `--color-cyan` | `#00e5ff` | Acento primario, CTAs, logo |
| `--color-violet` | `#8b5cf6` | Acento secundario |
| `--color-green` | `#00ffa3` | Indicadores de estado activo |
| `--color-ink` | `#e4e4f0` | Texto principal |
| `--color-muted` | `#5a5a7a` | Texto secundario (usar con cuidado en fondos oscuros) |

> **Lección aprendida:** `--color-muted` (`#5a5a7a`) tiene poco contraste sobre fondos oscuros. Para subtítulos en secciones oscuras usar `#a8a8c8` o superior.

### Tipografía

| Rol | Fuente | Pesos |
|---|---|---|
| Titulares (`--font-display`) | **Manrope** | 400–800 |
| Cuerpo (`--font-body`) | **Outfit** | 300–700 |
| Terminal/código (`--font-mono`) | **JetBrains Mono** | 400–600 |

**Por qué Manrope para titulares:**
- Se descartó **Syne** (opción inicial) porque a peso 800 resulta muy ancha y aplastada — difícil de leer en titulares largos
- **Manrope** tiene proporciones naturales, excelente legibilidad a cualquier tamaño y un caracter moderno/tech sin agresividad
- Se evitaron deliberadamente Inter, Roboto, Space Grotesk y Arial por ser demasiado genéricas

### Efectos visuales definidos
- **Dot grid background** — patrón de puntos cyan muy tenues, con máscara radial en bordes
- **Glassmorphism cards** — `backdrop-filter: blur(16px)` + borde semitransparente
- **Gradient text** — `linear-gradient(135deg, #00e5ff, #8b5cf6)` con `background-clip: text`
- **Glow borders** — `box-shadow` exterior con color del acento al 25% opacidad
- **Scan line** — línea cyan que barre la pantalla al cargar (CSS `@keyframes`)
- **Terminal animada** — líneas que aparecen secuencialmente con `animation-delay`

---

## Estructura de ficheros

```
pagina web/
├── .github/
│   └── workflows/
│       └── deploy.yml          ← GitHub Actions para Pages
├── public/
│   └── favicon.svg             ← SVG inline con gradiente cyan→violet
├── src/
│   ├── components/
│   │   ├── Header.astro        ← Nav fija, glassmorphism, menú móvil
│   │   ├── Hero.astro          ← Sección fullscreen con terminal animada
│   │   ├── WhatIs.astro        ← Stats + manifiesto (#que-es)
│   │   ├── HowItWorks.astro    ← Timeline 3 pasos (#como-funciona)
│   │   ├── Features.astro      ← Bento grid 6 cards (#beneficios)
│   │   ├── CTA.astro           ← Sección de unirse (#unete)
│   │   └── Footer.astro        ← Links + copyright
│   ├── layouts/
│   │   └── BaseLayout.astro    ← HTML shell, SEO meta, OG tags
│   ├── pages/
│   │   └── index.astro         ← Composición pura de componentes
│   └── styles/
│       └── global.css          ← Design system completo
├── .gitignore
├── astro.config.mjs
├── package.json
└── tsconfig.json
```

---

## Comandos

```bash
npm run dev      # Servidor de desarrollo → http://localhost:4321
npm run build    # Build estático → dist/
npm run preview  # Previsualizar el build
npm run check    # Type-check TypeScript
```

---

## Deploy

### GitHub Pages
1. Subir el código a un repositorio GitHub
2. Ir a **Settings → Pages → Source → GitHub Actions**
3. El workflow `.github/workflows/deploy.yml` se ejecuta automáticamente en cada push a `main`

> Si el repo **no** se llama `username.github.io`, ajustar en `astro.config.mjs`:
> ```js
> base: '/nombre-del-repo',
> ```

### Vercel
- Conectar el repositorio en el dashboard de Vercel
- Vercel auto-detecta Astro: build command `astro build`, output `dist/`
- No se necesita `vercel.json`

### CTA principal
El botón "Unirme" apunta a: `https://comunidad.metadigitales.com`

---

## Secciones de la landing page

| Sección | ID | Descripción |
|---|---|---|
| Hero | — | Titular principal + terminal animada + social proof |
| Qué es | `#que-es` | Stats + manifiesto de la comunidad |
| Cómo funciona | `#como-funciona` | Timeline de 3 pasos: Idea → Automatización → Escala |
| Beneficios | `#beneficios` | Bento grid con 6 features clave |
| Únete | `#unete` | CTA principal con métricas |
| Footer | — | Links de navegación y legales |
