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
