# Decisiones de MotorOS

Registro de decisiones importantes y su motivo, para que un asistente nuevo no las repita
ni las revierta sin darse cuenta.

## Producto y alcance

**Se expandió de "landing de SaaS" a "DMS completo de 12 módulos".**
El pedido original era una landing page premium para un producto llamado MotorOS. A mitad
de proyecto, el usuario pidió evolucionarlo a un DMS (Dealer Management System) completo
inspirado en Frazer, con arquitectura de 12 módulos + IA transversal. Se mantuvo todo el
trabajo anterior (landing) y se amplió en vez de reemplazarlo.

**GoAuto (goauto.cl) es el competidor directo real**, no GoAutos (goautos.com.ar, que es un
portal de clasificados de Córdoba, Argentina — un canal de publicación, no un competidor de
software). No confundir ambos nombres en el futuro.

## Infraestructura (GitHub / Vercel)

**El sandbox de Claude no tiene acceso de red a github.com, vercel.com ni npm** (bloqueado
por allowlist, confirmado con `curl` devolviendo `403 blocked-by-allowlist`). Por eso:
- Claude no puede hacer `git push` directamente — el commit queda local, y el usuario hace
  el push desde su propia terminal.
- Para instalar herramientas (ej. GitHub CLI `gh`), el usuario tuvo que instalar Homebrew
  primero (`brew install gh`) porque no tenía ningún gestor de paquetes.
- La autenticación de git se resolvió con `gh auth login` (login por navegador/OAuth), no
  con un Personal Access Token pegado a mano — Claude tiene una regla dura de no manejar
  contraseñas/tokens ni siquiera si el usuario los pasa directamente en el chat. En un punto
  el usuario pegó un token en el chat por error; se le indicó tratarlo como comprometido y
  revocarlo en GitHub inmediatamente.

## Fotos de vehículos

**Se descartó (1) generar/descargar fotos y (2) usar Google Images**, y se pasó por dos
etapas:
1. Ícono SVG único + fondo de "estudio fotográfico" consistente (gradiente + sombra de
   piso) — para tener consistencia total sin depender de fotos externas.
2. El usuario pidió fotos reales igual, así que se reemplazó por **fotos de stock de
   Unsplash** (licencia libre para uso comercial, sin descarga, enlazadas por URL), una por
   tipo de carrocería/vehículo, manteniendo el mismo marco visual (tamaño, recorte,
   degradado) para que se sientan parte de una misma galería.

Motivo de no usar Google Images: no es una fuente descargable de forma segura (no hay
herramienta de descarga de imágenes en el sandbox) y las fotos indexadas por Google
Images son de terceros con copyright, no aptas para un sitio comercial sin licencia.

## Inventario y datos de demo

**Se pasó de 6 vehículos hardcodeados a un generador de 140 vehículos con semilla fija**
(8 Ingreso, 14 Preparación, 61 Publicado, 17 Reservado, 40 Vendido), porque el usuario pidió
que el inventario y las ventas "tuvieran sentido" como una empresa real con volumen. La
semilla fija (`seededRandom`) asegura que los números no cambien entre recargas de página,
aunque no hay persistencia real (no es una base de datos).

**El Kanban de Inventario limita a 4 tarjetas por columna + "+N más"** en vez de mostrar
las 61 tarjetas de "Publicado" de una: con 140 vehículos, mostrar todo sin límite rompía la
usabilidad. El "+N más" filtra la lista de fichas completas de abajo.

**Inventario muestra fichas completas para cada vehículo (no una tabla comprimida)**, con
barra de filtros por estado + buscador + paginación ("Cargar más vehículos"), porque el
usuario mostró una captura de referencia (estilo Frazer/portal de autos) y pidió que todos
los vehículos se vieran con ese nivel de detalle, no solo el seleccionado.

## Login y plataforma

**Se agregó `login.html` como paso previo a `app.html`**, con la estética propia de MotorOS
(dark premium) en vez de imitar el diseño de portal.goauto.cl — ese sitio es una app que
carga por JavaScript y no se pudo inspeccionar (sin navegador Chrome conectado en la
sesión). Es un login 100% demo: cualquier correo/contraseña entra, no hay backend.

## Sidebar compacta con hover-expand (sin mover el contenido principal)

El sidebar de `app.html` pasó de "siempre 236px, texto + ícono visibles" a "80px compacto
(solo ícono) por defecto en escritorio, expandido a 260px al pasar el cursor". Decisión de
implementación clave: **el sidebar es `position:fixed`, no un hijo normal del flex de
`.app-shell`**, y `.app-content` tiene un `margin-left:80px` fijo que nunca cambia. Así, la
expansión a 260px es un overlay que se monta encima del contenido (con box-shadow), y el
layout principal nunca se recalcula ni salta — si en el futuro se cambia el ancho compacto o
expandido, hay que actualizar el `margin-left` de `.app-content` para que siga coincidiendo
con el ancho compacto.

Se envolvió el texto de cada ítem en `<span class="nav-label">` (mismo texto exacto, sin
agregar/quitar contenido) porque sin ese wrapper no es posible animar el fade-in/desplazamiento
del texto por separado del ícono — es un detalle de implementación, no un cambio de contenido.
Mobile (<=860px) no se tocó en su comportamiento: sigue siendo el cajón de 260px con botón
hamburguesa que ya existía.

## Iconografía: Lucide Icons (reemplazó todos los emojis)

El usuario pidió usar **exclusivamente Lucide Icons** en toda la interfaz (nav, botones,
alertas, chat, etc.), nada de emojis ni de imágenes genéricas, con trazo fino y estilo
consistente. Se implementó así, dado que el sitio no tiene build step:

- **Lucide se carga vía CDN** (`<script src="https://unpkg.com/lucide@latest">`), no vía
  npm/build — es la forma correcta de usarlo en un sitio estático sin bundler. Esto agrega
  **una dependencia de red en tiempo de ejecución** que el sitio no tenía antes (antes todo
  era 100% autocontenido salvo Google Fonts). Si algún día se quiere volver a cero
  dependencias externas, la alternativa sería incrustar un sprite SVG local con los `<path>`
  exactos de cada ícono usado (mismo patrón que ya se usaba para el ícono de auto antes de
  tener fotos reales).
- Cada ícono se marca como `<i data-lucide="nombre-en-kebab-case" style="width:Npx;height:Npx;">`
  y `lucide.createIcons()` los reemplaza por el SVG real. **Importante:** como varias partes
  de la interfaz generan contenido con `innerHTML` dinámicamente después de la carga inicial
  (chat del widget/vista IA, lista de inventario filtrada, detalle de un lead del CRM), hay
  que llamar `lucide.createIcons()` de nuevo **dentro de esas funciones**, no solo una vez al
  final de la carga — si se agrega un ícono nuevo a un lugar que se re-renderiza
  dinámicamente, hay que acordarse de este detalle o el ícono no aparece.
- No se tocaron: el número `⌘K` (atajo de teclado, es texto convencional no un ícono), el
  punto `●` del stepper de financiamiento (indicador de "paso actual", no es un ícono con
  significado propio), ni el nombre de marca "M" del logo.
- **La imagen del robot (`images/chatbot.png`) se reemplazó por el ícono `Bot` de Lucide**
  en todos los avatares del chat — la imagen contradecía la regla de "solo Lucide, nada de
  imágenes". El archivo **sigue en el repo sin usar** (no se borró sin preguntar); si ya no
  hace falta, se puede eliminar.

## Mascota del chatbot (MotorOS AI)

El usuario subió `images/chatbot.png` (un robot ilustrado cargando un auto) para usar como
ícono del asistente MotorOS AI en vez del emoji 🤖. La imagen original tenía fondo blanco
y pesaba 973KB; se procesó con PIL (umbral de blanco → transparencia, recorte al bounding
box del contenido, resize a 300px máx.) quedando en ~116KB con fondo transparente.

Se reemplazó el emoji específicamente en: el botón flotante del widget, los avatares de
mensajes de IA en ambos chats (widget y vista dedicada de `app.html`), y el mockup de chat
del módulo 12 en `index.html`. **No** se tocaron los usos de 🤖 en otros contextos (etiquetas
"MOTOROS INSIGHTS" / "IA COMERCIAL", ícono chico de 16px del sidebar) — esos son elementos
de texto distintos, no el avatar del chatbot.

## Cómo entregar imágenes reales a Claude (importante, causó confusión una vez)

**Las imágenes pegadas o adjuntadas directamente en el chat NO quedan como archivo
accesible para Claude** — Claude solo las "ve" como parte de la conversación, pero no hay
un archivo en disco que pueda copiar (se verificó que la carpeta de uploads del sandbox
queda vacía en ese caso). La única forma que funciona es que el usuario **arrastre el
archivo directamente a la carpeta conectada** (`~/CLAUDE/...`, cualquier subcarpeta) usando
Finder — ahí sí Claude puede leerlo, moverlo, comprimirlo, etc.

**Las imágenes que suba el usuario deben comprimirse antes de commitear** si van a mostrarse
como miniaturas pequeñas: no tiene sentido subir un screenshot de pantalla completa de varios
MB para una foto de 92×92px. Se usó PIL (`Image.resize` + guardar como JPEG calidad ~78,
máximo 600px de ancho) — una imagen de 4.4MB bajó a ~46KB sin pérdida visible a ese tamaño.
Esto es una buena práctica a repetir con cualquier imagen nueva que se agregue al sitio.

## Sección "El problema" — de emojis a collage a fila chica

Se rediseñó dos veces sobre la misma idea (5 imágenes: Frazer, Excel, fotos desordenadas de
vehículos, Facebook Marketplace, clientes sin seguimiento — que el usuario sube él mismo):

1. Primero, de tarjetas con emoji a un **collage grande de fotos superpuestas y rotadas**
   (estilo "caos operativo").
2. El usuario pidió algo más chico y "que se vea rápido" — se cambió a una **fila compacta
   de 5 miniaturas (92×92px) a la derecha del título**, con el texto a la izquierda. Esta es
   la versión vigente. Si se vuelve a pedir un cambio de layout de esta sección, no asumir
   que el collage grande es lo correcto — la fila chica es la decisión más reciente.

**Los `<img>` usan `onerror` para degradarse a un placeholder con patrón diagonal + ícono**
en vez de mostrar el ícono nativo de "imagen rota" del navegador. Esto es intencional: como
las imágenes reales todavía no existen en el repo, el sitio se ve terminado e intencional
igual mientras el usuario las sube. Los nombres de archivo exactos esperados están en
`images/README.md` — si se agregan imágenes con otro nombre, hay que actualizar el `src` en
`index.html` o renombrar los archivos para que coincidan.

## Módulos conectados al generador de datos

**Se conectaron Compras, Financiamiento, Documentos, Contabilidad y Reportes al mismo
generador de 140 vehículos** (en dos etapas), en vez de dejarlos con contenido de ejemplo
estático que no correspondía a ningún dato real del sistema. Reglas aplicadas para decidir
qué mostrar en cada uno (para que un asistente futuro no las reinvente distinto):

- **Compras**: cada vehículo tiene un origen de compra (tipo, contraparte, estado de pago,
  días desde el ingreso) generado junto con el resto de sus datos. La vista muestra las 20
  adquisiciones más recientes.
- **Financiamiento**: solo los vehículos en estado "Reservado" generan un caso de crédito
  activo (con banco, monto, plazo, cuota y etapa Enviada/Evaluación/Aprobado — nunca
  "Firmado", porque si el crédito ya se firmó, el vehículo pasaría a "Vendido").
- **Documentos**: se generan desde eventos reales — nota de venta y documentos de entrega
  para cada vehículo vendido; contrato de compra o de consignación según el tipo de
  adquisición de cada vehículo. Se muestran los 20 más recientes de un total mayor.
- **Contabilidad**: Ventas, Margen, Comisiones y Flujo de caja neto se calculan sumando los
  40 vehículos vendidos (no son cifras sueltas). El "flujo de caja neto" es una aproximación
  (ventas − costo total − comisiones), no un balance contable real.
- **Reportes**: se agregó una tabla real de "Top 10 vehículos por rentabilidad" (por
  `marginFinal`), además del gráfico de barras decorativo que ya existía.

**Marketing y Configuración se dejaron sin conectar a propósito**: Marketing muestra
campañas de ejemplo que no tienen un mapeo natural a los datos de vehículos (son campañas
publicitarias, no transacciones), y Configuración es un formulario de ajustes de la cuenta,
no un listado de datos. Si se quiere conectar igual, hay que definir primero qué
significaría "dato real" en esos dos casos.

## MotorOS AI

**Se agregó un widget flotante de MotorOS AI disponible en toda la plataforma** (no solo en
su sección dedicada del sidebar), porque el usuario lo pidió explícitamente como "ventanita
de chat siempre abajo de la página". Incluye un selector de rol (Dueño / Vendedor) como
demostración tangible de que "la IA conoce todo el proyecto pero contesta con limitaciones,
excepto el dueño" — las preguntas financieras sensibles (ganancias, comisiones) muestran un
aviso de acceso restringido cuando el rol no es Dueño.

## Identidad de marca: logo vectorial y su instalación

**El logo se construyó a mano en SVG (paths/polygons con degradado), no se generó como
imagen ni se trazó de una foto** — el usuario pidió explícitamente "un logo completamente
editable", así que cada variante (ícono solo, lockup horizontal, lockup vertical, mono
blanco/negro) vive como archivos `.svg` de texto plano en `brand/`.

**Instalación en el sitio: `<symbol>` SVG oculto + `<use>`, no `<img>`.** El ícono se define
una sola vez por archivo HTML (bloque `<svg width="0" height="0" style="position:absolute">`
con el `<symbol id="motorosLogo">` y su gradiente) y se reutiliza con
`<svg><use href="#motorosLogo"/></svg>` en cada lugar donde aparecía el viejo cuadrado con la
letra "M" (header de `index.html`, `login.html`, sidebar y preview interno de `app.html`).
Ventaja sobre usar un archivo de imagen: cero requests HTTP extra, se puede re-colorear con
CSS si hace falta, y es coherente con que todo el sitio es HTML autocontenido.

**Centrado del logo/íconos en el sidebar compacto: colapsar `max-width` a 0, no solo
`opacity`.** El bug que reportó el usuario ("no recortes ni muestres parcialmente MotorOS")
pasaba porque el texto seguía ocupando espacio en el layout (solo se volvía invisible con
`opacity:0`), y el `overflow:hidden` del sidebar lo cortaba a la mitad en vez de ocultarlo
del todo. Además, con `gap` fijo entre ícono y texto, un texto "invisible pero con ancho"
descentraba visualmente al ícono solo. Se corrigió con:
1. `max-width:0; overflow:hidden;` además de `opacity:0` en las etiquetas de texto (logo,
   nav items, pie del sidebar) — así el texto realmente no ocupa espacio en compacto.
2. `justify-content:center` por defecto (compacto) y `justify-content:flex-start` en
   `.app-sidebar:hover` (expandido), tanto en el logo como en cada `.app-nav-item` y en el
   pie del sidebar — así el ícono queda perfectamente centrado en la barra de 80px, y pasa a
   alineación izquierda junto al texto al expandir a 260px.
Este mismo fix se aplicó también al pie (avatar + info + botón salir) aunque el usuario no
lo pidió explícitamente para esa zona, porque tenía el mismo bug de recorte parcial.
