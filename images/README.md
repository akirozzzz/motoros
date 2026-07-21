# Imágenes de la sección "El problema" (landing)

Esta carpeta debe contener las 5 imágenes que arma el collage de la sección de problema en
`index.html` (id `#problema`). Los nombres de archivo deben ser exactos (son los que ya están
referenciados en el HTML):

| Archivo | Qué debe mostrar | Etiqueta en el collage |
|---|---|---|
| `problema-frazer.jpg` | Captura de pantalla de un DMS antiguo (ej. Frazer) | "Frazer" |
| `problema-excel.jpg` | Planilla Excel de inventario/precios | "Excel" |
| `problema-fotos-dispersas.jpg` | Fotos de vehículos desordenadas/duplicadas | "Fotos dispersas" |
| `problema-facebook-marketplace.jpg` | Publicación de un auto en Facebook Marketplace | "Facebook Marketplace" |
| `problema-sin-seguimiento.jpg` | Algo que comunique "cliente sin seguimiento" (ej. chat de WhatsApp sin responder, lista de leads con fechas vencidas) | "Sin seguimiento" |

## Especificación técnica

- Formato: `.jpg` o `.png` (si usas otra extensión, hay que actualizar el `src` en `index.html`).
- Tamaño recomendado: mínimo 800×600px, orientación horizontal preferida (se recortan con
  `object-fit: cover`, así que no necesitan ser exactamente cuadradas ni del mismo tamaño
  entre sí).
- Mientras no exista el archivo, la tarjeta muestra automáticamente un placeholder con
  patrón diagonal y un ícono de imagen — no se ve como un ícono de "imagen rota" del
  navegador, así que la sección se ve bien igual antes de que subas las fotos reales.

## Cómo agregarlas

1. Copia los 5 archivos con esos nombres exactos dentro de esta carpeta (`images/`).
2. Haz commit y push como cualquier otro cambio:
   ```
   cd ~/CLAUDE/motoros
   git add images/
   git commit -m "Agregar imagenes reales de la seccion problema"
   git push
   ```
3. Vercel las toma automáticamente en el próximo deploy — no hace falta tocar el HTML.
