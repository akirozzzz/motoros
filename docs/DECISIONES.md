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

## MotorOS AI

**Se agregó un widget flotante de MotorOS AI disponible en toda la plataforma** (no solo en
su sección dedicada del sidebar), porque el usuario lo pidió explícitamente como "ventanita
de chat siempre abajo de la página". Incluye un selector de rol (Dueño / Vendedor) como
demostración tangible de que "la IA conoce todo el proyecto pero contesta con limitaciones,
excepto el dueño" — las preguntas financieras sensibles (ganancias, comisiones) muestran un
aviso de acceso restringido cuando el rol no es Dueño.
