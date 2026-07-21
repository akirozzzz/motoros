# Pendientes de MotorOS

Última actualización: al crear este archivo (ver `git log` para fecha exacta del commit).

## Estado actual

El repo está en `main`, sincronizado con GitHub (`github.com/akirozzzz/motoros`) y
desplegado en Vercel (`motoros-sandy.vercel.app`). El working tree está limpio (sin cambios
sin comitear) al momento de escribir esto.

Lo que ya funciona de punta a punta:
- Landing (`index.html`) completa, con los 12 módulos explorables.
- Login (`login.html`) → redirige a `app.html` (sin autenticación real).
- Producto interno (`app.html`): Dashboard, Inventario (Kanban + fichas + filtros/búsqueda/
  paginación), CRM (Kanban de leads), Ventas (generada desde vehículos vendidos), MotorOS AI
  (chat de página completa + widget flotante con demo de restricción por rol).
- Datos de demo consistentes: 140 vehículos (100 activos + 40 vendidos) generados con
  semilla fija.
- Responsive: sidebar de `app.html` con drawer mobile (hamburguesa), tablas con scroll
  horizontal, Kanban con scroll horizontal en pantallas chicas.

## Bugs / limitaciones conocidas (no son errores accidentales, son limitaciones aceptadas)

- **Fotos de vehículos son genéricas**, no el modelo/año/color exacto de cada ficha. Viene
  de una limitación real: no hay forma de descargar ni generar imágenes en el sandbox de
  Claude, y usar fotos scrapeadas de Google Images tiene riesgo de copyright. Si se quiere
  resolver de verdad, hay que conseguir una fuente de fotos con licencia por modelo (API de
  catálogo automotriz, o que el cliente suba sus propias fotos).
- **Login es 100% demo**: cualquier correo/contraseña entra. No hay backend de
  autenticación real.
- **No hay backend real en ningún módulo**: todo el inventario/ventas/CRM vive en arrays de
  JS que se regeneran en cada carga de página. No hay persistencia real entre sesiones de
  usuario ni sincronización multi-dispositivo.
- **Vercel solo tiene conectados `index.html` (raíz) y `app.html`/`login.html` como páginas
  estáticas sueltas** — no hay router ni SPA, cada archivo es una página independiente.
- El widget de MotorOS AI y el chat de la vista dedicada "MotorOS AI" tienen lógica de
  respuestas **separada y duplicada** (dos diccionarios de preguntas/respuestas distintos).
  Si se agregan más respuestas, hay que decidir si conviene unificarlos.
- Módulos del sidebar de `app.html` que **todavía no tienen datos dinámicos** (contenido
  de ejemplo estático, no conectado al generador de inventario): **Marketing** (campañas
  por canal — no tiene un mapeo natural a datos de vehículos) y **Configuración** (formulario
  de ajustes, no es "dato" que conectar). El resto de los módulos (Dashboard, Inventario,
  Compras, Ventas, CRM, Financiamiento, Documentos, Contabilidad, Reportes) ya están
  conectados al generador de 140 vehículos.

## Etapa reciente: identidad de marca (logo vectorial) instalada + sidebar afinada

Se creó un set completo de identidad de marca (`brand/`, además copiado a
`/Users/akirozzz/CLAUDE/motoros/brand/` en el repo): ícono "M" vectorial (líneas de
movimiento que se transforman en un trazo sólido, degradado azul), lockups horizontal y
vertical, variantes claras/oscuras/transparentes/monocromáticas, favicons en 6 tamaños y
`brand/BRAND-GUIDELINES.md` con colores/tipografía/uso. El ícono se instaló como un
`<symbol>` SVG inline (reutilizado vía `<use>`) en `index.html`, `login.html` y `app.html`,
reemplazando el cuadrado con la letra "M" en CSS. **No se agregó todavía el favicon `<link>`
en el `<head>`** (los PNG ya están listos en `brand/favicons/`, falta solo enlazarlos si se
quiere).

Además se ajustó el sidebar de `app.html` a pedido explícito del usuario:
- El logo en modo compacto ahora muestra solo la "M" **centrada** (antes el texto "MotorOS"
  quedaba parcialmente cortado por el `overflow:hidden` en vez de ocultarse limpiamente —
  se corrigió colapsando su ancho a 0, no solo su opacidad).
- Mismo fix aplicado también al pie del sidebar (avatar + info + botón de salir), que tenía
  el mismo problema de recorte parcial aunque no se pidió explícitamente — se corrigió por
  consistencia con la regla "no mostrar nada parcialmente cortado".
- Íconos del menú aumentados de 16px a 30px, con más padding vertical (11px) para que respiren.
- Todos los ítems del nav (y el logo) ahora se centran en modo compacto y pasan a alineación
  izquierda al expandir, con transición de 200-250ms en ancho/opacidad/posición.

## Etapa reciente: sidebar compacta con hover-expand

`app.html`: el sidebar ahora es compacto (80px, solo íconos) por defecto en escritorio y se
expande a 260px al pasar el cursor, como overlay (no mueve el contenido principal). En mobile
sigue igual (cajón de 260px con botón hamburguesa). Ver `docs/DECISIONES.md` para el detalle
de implementación (`position:fixed` + `margin-left` fijo en `.app-content`). Commit `584015a`.

## Etapa reciente: iconografía Lucide

Se reemplazaron todos los emojis de la interfaz (nav, topbar, alertas, chat, etc.) por
íconos de Lucide (SVG vía CDN). Ver `docs/DECISIONES.md` para el detalle técnico y el
recordatorio importante sobre llamar `lucide.createIcons()` en contenido dinámico. Commit
`e303f14`.

**Corrección:** `images/chatbot.png` **sí sigue en uso** — la nota anterior estaba
desactualizada. Solo se migraron a Lucide los íconos `Bot` chicos dentro de las burbujas de
chat; el botón flotante circular (`#aiWidgetBtn`, la mascota robot completa) sigue usando
`images/chatbot.png` a propósito, ahora con animación (ver etapa siguiente).

## Etapa reciente: animación del botón flotante de MotorOS AI

El usuario subió un video (`chatbot.mp4`) para usar como GIF animado del botón del chat,
pero tenía marca de agua de **Iconscout** visible (asset de stock sin licencia comprada) —
se le avisó el riesgo y eligió que se hiciera una animación original en su lugar, sin
depender de ese archivo. Se implementó con CSS puro sobre la imagen ya existente
(`images/chatbot.png`):
- Animación continua de "flote" suave (`botIdleFloat`, sube/baja y rota levemente en loop).
- Al apretar el botón, un salto hacia arriba con rebote (`botRiseUp`) antes de que se abra el
  panel del chat.
- Ambas respetan `prefers-reduced-motion` (no se activan si el usuario tiene animaciones
  reducidas en su sistema).
El archivo `chatbot.mp4` original no se usó ni se copió al repo.

## Próximos pasos sugeridos (sin confirmar con el usuario todavía — ver primero con él)

En orden de impacto probable:

0. **Agregar las 5 imágenes reales de la sección "El problema"** en `images/` (ver
   `images/README.md` para nombres exactos y especificación). Hasta que no se suban, la
   sección se ve con placeholders con patrón diagonal en vez de las fotos reales.
1. **Decidir si Marketing y Configuración necesitan conectarse a algo real**, o si se dejan
   como están (campañas de ejemplo y formulario de ajustes respectivamente) porque no tienen
   un mapeo natural a los datos de vehículos.
2. **Definir si el login necesita algo más real** (aunque sea simulado con localStorage) o
   si se deja como demo pura a propósito.
3. **Decidir la estrategia de fotos de vehículos a largo plazo** (subir fotos propias del
   cliente, contratar una API de catálogo, o aceptar las fotos de stock genéricas como
   definitivas).
4. **Revisar accesibilidad** (contraste, navegación por teclado, etiquetas ARIA) — todavía
   no se ha hecho una pasada dedicada a esto.
5. Confirmar si se quiere unificar la lógica de MotorOS AI (widget + vista dedicada) en una
   sola fuente de respuestas.

## Historial de etapas completadas

- **Etapa 1 — Compras conectada al inventario real.** Cada vehículo generado tiene un
  origen de compra (tipo, contraparte, estado de pago, días desde el ingreso). La vista
  Compras muestra las 20 adquisiciones más recientes de las 140 totales. Commit `6e973ef`.
- **Etapa 2 — Financiamiento, Documentos, Contabilidad y Reportes conectados.**
  Financiamiento genera un caso de crédito por cada vehículo Reservado; Documentos genera
  notas de venta/contratos desde ventas y adquisiciones reales; Contabilidad calcula Ventas/
  Margen/Comisiones/Flujo de caja sumando los 40 vendidos; Reportes agregó una tabla de
  "Top 10 vehículos por rentabilidad". Verificado con balance de tags, sintaxis JS y una
  corrida en Node con DOM simulado (sin excepciones). Commit `89910fc`.
- **Etapa 3 — Botón "Iniciar sesión" en el header de la landing.** Enlace de texto simple
  junto a "Ver plataforma" (ambos llevan a `login.html` por ahora, es intencional). Se oculta
  junto con el nav central en mobile (<=760px) para no saturar el header. Commit `6004fa7`.
- **Etapa 4 — Imágenes en la sección "El problema" (varias iteraciones de diseño).**
  Collage grande superpuesto → fila compacta con etiquetas de texto → fila sin etiquetas →
  espacios sin imagen se ocultan (sin placeholder) → **imágenes más grandes (170×170px,
  120×120px en mobile) y más separadas en el stack de Frazer/Facebook**, porque la de atrás
  casi no se veía. El usuario subió **Frazer** y **Facebook Marketplace** a
  `~/CLAUDE/motoros/images/` (vía Finder), se comprimieron (una pesaba 4.4MB) y se muestran
  apiladas como dos hojas superpuestas, sin texto encima. Commits `348e1e6`, `5dc6203`,
  `7209015`, `225e3dd`, `4e8d0b0`, `541fa21`. **Pendiente: faltan 3 imágenes reales** (Excel,
  Fotos dispersas, Sin seguimiento) — esos espacios no se muestran hasta que se agreguen. Ver
  `images/README.md`.
- **Fix — placeholder de imágenes no se veía.** El `onerror` de los `<img>` llamaba
  `this.remove()` antes de leer `this.parentElement`, y al remover el nodo se pierde la
  referencia al padre — la clase `img-missing` nunca se aplicaba y la tarjeta quedaba
  vacía. Se corrigió el orden de las instrucciones. **Ojo para el futuro:** si se agrega
  un patrón similar de "fallback de imagen con onerror" en otro lado del sitio, usar
  siempre el orden "leer/usar el padre → después remover", nunca al revés. Commit `2b97d1d`.

## Cómo publicar cambios (recordatorio para el asistente)

Los cambios quedan comiteados localmente en `~/CLAUDE/motoros` pero **no se hace push
automático**. El usuario corre esto en su propia terminal cuando quiera publicar:

```
cd ~/CLAUDE/motoros
git push
```

Ya tiene `gh` (GitHub CLI) instalado y autenticado en su máquina, así que el push no debería
pedir usuario/token de nuevo salvo que expire la sesión.
