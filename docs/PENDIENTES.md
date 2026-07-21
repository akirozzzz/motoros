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

## Etapa reciente: logo recalcado del mockup original + sidebar con scroll interno

El usuario avisó que la "M" instalada en la etapa anterior no se parecía al mockup real que
había enviado (esa imagen ya no estaba disponible para Claude, se había subido en una sesión
anterior y no persiste entre sesiones). El usuario volvió a compartir la imagen y se
**recalcó el símbolo desde cero** para que coincida fielmente:
- Antes: trazo grueso con puntas **redondeadas** (`stroke-linecap:round`, `stroke-linejoin:round`),
  M simétrica, sin inclinación.
- Ahora: puntas **en ángulo recto/a inglete** (`stroke-linecap:butt`, `stroke-linejoin:miter`),
  pata derecha más alta que la izquierda (asimetría "despegando"), y **todo el símbolo + el
  wordmark llevan `skewX(-10)`** para el efecto "cursiva/velocidad" que pidió el usuario
  explícitamente. Ver `brand/BRAND-GUIDELINES.md` para el detalle exacto.
- Se actualizó el `<symbol id="motorosLogo">` en los 3 HTML (mismo mecanismo de `<use>`, solo
  cambió el contenido del symbol) y se regeneraron todos los archivos de `brand/` (SVGs +
  favicons) con el nuevo diseño.

**Aprendizaje para el futuro:** las imágenes que el usuario sube en el chat (no vía Finder a
la carpeta conectada) **no persisten entre sesiones/compactaciones** — si se necesita
recrear algo desde una imagen de referencia más adelante, pedirle que la vuelva a compartir
en vez de asumir que sigue disponible.

También se corrigió un bug real que el usuario reportó: el menú del sidebar en `app.html` se
cortaba abajo en pantallas de altura normal (13 ítems + más padding vertical, por el aumento
de íconos a 30px de la etapa anterior, ya no entraban en 100vh). Se envolvió el listado de
navegación en `.app-nav-scroll` (`flex:1; overflow-y:auto`), dejando el logo fijo arriba y el
pie fijo abajo.

**El usuario avisó que seguía cortándose** después del primer intento (flex + min-height:0).
Como Claude no tiene forma de abrir un navegador real en este sandbox para verificar
visualmente el resultado (no hay Chromium/Playwright instalado y no se puede instalar sin
red), se reforzó el fix con un patrón más a prueba de balas en vez de solo confiar en la
lectura del CSS:
- El sidebar pasó de `flex` a **CSS Grid** (`grid-template-rows: auto minmax(0,1fr) auto`)
  para el patrón logo-fijo / menú-scrolleable / usuario-fijo — más confiable entre
  navegadores que flex+min-height:0.
- Se recortó el padding vertical de los ítems (11px→8px) y de las etiquetas de grupo para
  que en la mayoría de las pantallas entre todo sin necesitar scroll.
- Se agregó un **difuminado visual (mask-image)** abajo del listado que avisa cuando hay más
  opciones para scrollear (y se apaga solo con JS al llegar al final, para no tapar
  "Configuración", el último ítem) — así, si en alguna pantalla todavía hace falta scrollear,
  al menos es obvio que se puede, en vez de parecer que el contenido está roto/perdido.
Si después de este cambio el problema persiste, lo más rápido para diagnosticarlo es que el
usuario mande una captura de pantalla del sidebar tal cual se ve cortado.

## Etapa reciente: mascota propia del chatbot (reemplaza images/chatbot.png)

El usuario aclaró que quería que el botón flotante de MotorOS AI se viera como el "monito"
del video que subió (cara redonda, antena, pantalla con dos ojitos) — no la imagen de
"robot levantando un auto" que se venía usando. Como el video tenía marca de agua de
Iconscout (ver etapa anterior de la animación), se diseñó una **mascota 100% original en
SVG** inspirada en ese estilo genérico de robot (cabeza redonda, antena con bolita, orejas,
pantalla con dos ojos) pero con la paleta de marca de MotorOS (degradado azul + ojos en
verde menta `--accent-2`), en vez de naranja. Se instaló como `<symbol id="motorosBot">`
(mismo mecanismo que el logo) en `app.html`, reemplazando el `<img src="images/chatbot.png">`
del botón flotante. Mantiene las animaciones de flote/salto ya construidas, ahora apuntando
al SVG en vez de a la imagen.

**`images/chatbot.png` ahora sí quedó completamente sin uso** en el repo. No se borró
todavía (no hay que eliminar archivos sin preguntar) — confirmar con el usuario si se puede
quitar.

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
