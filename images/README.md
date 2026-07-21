# Imágenes de la sección "El problema" (landing)

Esta carpeta contiene las imágenes de la fila de "sistemas antiguos" en la sección de
problema de `index.html` (id `#problema`). Los nombres de archivo deben ser exactos (son los
que ya están referenciados en el HTML):

| Archivo | Estado | Qué muestra |
|---|---|---|
| `problema-frazer.jpg` | ✅ Agregada | Captura de un DMS antiguo (Frazer) — se muestra apilada con la de Facebook |
| `problema-facebook-marketplace.jpg` | ✅ Agregada | Publicación de autos en Facebook Marketplace — se muestra apilada con la de Frazer |
| `problema-excel.jpg` | ⏳ Pendiente | Planilla Excel de inventario/precios |
| `problema-fotos-dispersas.jpg` | ⏳ Pendiente | Fotos de vehículos desordenadas/duplicadas |
| `problema-sin-seguimiento.jpg` | ⏳ Pendiente | Algo que comunique "cliente sin seguimiento" (ej. chat sin responder, leads vencidos) |

Frazer y Facebook Marketplace se muestran **apiladas como dos hojas superpuestas** (una
levemente rotada detrás de la otra), no como cuadros separados — es el diseño pedido por el
usuario. Las 3 restantes se muestran como cuadros individuales en la misma fila.

## Especificación técnica

- Formato: `.jpg` (si se agrega en otro formato, hay que actualizar el `src` en `index.html`
  o convertir el archivo).
- **Las imágenes se comprimen antes de subirlas** (con PIL/ImageMagick): máximo 600px de
  ancho, calidad JPEG ~78. Las fotos se muestran en miniaturas de 92×92px, así que no tiene
  sentido subir archivos de varios MB — enlentece la carga de la página sin mejorar nada
  visualmente. `problema-facebook-marketplace.jpg` originalmente pesaba 4.4MB (un screenshot
  de pantalla completa) y se comprimió a ~46KB sin pérdida visible a ese tamaño.
- Mientras no exista un archivo (Excel, Fotos dispersas, Sin seguimiento), ese espacio
  simplemente **no se muestra** (el cuadro se oculta por completo, sin placeholder ni ícono).
  La fila se ve más corta con solo las imágenes que sí existen, hasta que agregues las que
  faltan.

## Cómo agregar las que faltan

1. Guarda el archivo en esta carpeta (`images/`) con el nombre exacto de la tabla de arriba.
2. Si el archivo es grande (captura de pantalla completa, foto de celular sin comprimir),
   pídele a Claude que lo comprima antes de commitear — no hace falta hacerlo a mano.
3. Commit y push:
   ```
   cd ~/CLAUDE/motoros
   git add images/
   git commit -m "Agregar imagenes reales de la seccion problema"
   git push
   ```
4. Vercel las toma automáticamente en el próximo deploy — no hace falta tocar el HTML.
