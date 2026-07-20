# MotorOS

El sistema operativo inteligente para automotoras. Landing page premium y prototipo funcional del producto (DMS + IA).

## Estructura del repo

```
index.html        → Landing page pública (hero, problema, 12 módulos, plataforma, CTA final)
app.html          → Prototipo interactivo del producto interno (sidebar de 13 secciones,
                     Dashboard, Inventario, CRM y MotorOS AI son funcionales)
docs/
  estrategia-y-copy.md   → Narrativa de marca, copy final y guion del video de lanzamiento
  motoros-prd.docx        → Especificación de producto (PRD): arquitectura, 12 módulos,
                             flujos de datos y diseño funcional de MotorOS AI
```

Ambos `.html` son archivos estáticos autocontenidos (sin build step): todo el CSS y JS
están inline, y las fuentes se cargan desde Google Fonts vía CDN.

## Ver en local

Abre `index.html` o `app.html` directamente en el navegador, o sirve la carpeta con
cualquier servidor estático:

```bash
npx serve .
```

## Deploy en Vercel

No requiere configuración: al importar este repo en Vercel
([vercel.com/new](https://vercel.com/new)), lo detecta como sitio estático y publica
`index.html` en la raíz. Cada push a `main` genera un nuevo deploy automáticamente.

## Cómo seguir iterando

Los cambios a la landing y al prototipo se hacen directamente sobre `index.html` /
`app.html` y se suben a `main` con un mensaje de commit claro describiendo qué se
modificó.
