# Contexto de MotorOS

## Qué es

MotorOS es "el sistema operativo inteligente para automotoras" — un DMS (Dealer Management
System) evolucionado con IA, dirigido a automotoras independientes y medianas (mercado
principal de referencia: Chile / LatAm hispanohablante). No es un CRM genérico, ni un ERP
tradicional, ni un simple inventario digital: conecta compra, inventario, ventas, marketing,
financiamiento, documentos y contabilidad en un solo sistema, con una capa de IA
(MotorOS AI) que analiza, recomienda y automatiza.

Referencia competitiva directa: **GoAuto** (goauto.cl), software chileno de gestión
automotriz con IA (tasación instantánea, CRM, marketing automatizado). También se investigó
**GoAutos** (goautos.com.ar), que es un marketplace/portal de clasificados de autos en
Córdoba, Argentina (no es competidor directo, es un canal de publicación).

Inspiración de diseño explícita del usuario: Tesla, Stripe, Linear, Vercel, HubSpot, Notion.
Referencia de profundidad funcional: Frazer (DMS clásico de EE.UU.), pero con UI moderna.

## Usuarios

- **Dueño de automotora** (rol principal simulado en el prototipo: "Ariel Quiroz — Dueño ·
  Automotora Demo"): acceso completo, incluyendo datos financieros sensibles.
- **Vendedor**: rol con acceso restringido a ciertas preguntas financieras (demostrado en el
  widget de MotorOS AI con un selector de rol).
- Cliente final (comprador de autos): no tiene interfaz propia todavía, solo aparece como
  "lead" dentro del CRM.

## Los 12 módulos (definidos en el PRD, `docs/motoros-prd.docx`)

01 Dashboard Ejecutivo · 02 Inventario Vehicular · 03 Carga Inteligente con Patente + IA ·
04 Compras y Adquisiciones · 05 CRM Automotriz · 06 Ventas y Cierre · 07 Financiamiento ·
08 Documentos · 09 Publicación Multicanal · 10 Página Web Automática · 11 Contabilidad y
Reportes · 12 MotorOS AI (copiloto).

## Estructura de archivos y qué representa cada uno

- **`index.html`** — Landing pública de marketing. Hero con dashboard animado, sección de
  problema (Excel/WhatsApp/portales sueltos), explorador de los 12 módulos en tabs, preview
  de la plataforma interna, CTA final. Público objetivo: dueños de automotora evaluando
  contratar MotorOS.
- **`app.html`** — Prototipo funcional del producto real (la plataforma que usaría el
  dueño/vendedor logueado). Sidebar con 13 secciones. Las secciones más desarrolladas:
  Dashboard, Inventario (Kanban + fichas completas + filtros/búsqueda/paginación), Clientes
  CRM (Kanban de 7 etapas), Ventas, Publicaciones, Configuración, y MotorOS AI (chat de
  página completa + widget flotante disponible en toda la app).
- **`login.html`** — Pantalla de login antes de entrar a `app.html`. Es una demo sin
  autenticación real: cualquier correo/contraseña deja pasar.

## Datos de demo (no son datos reales de ningún cliente)

`app.html` genera su propio inventario en el navegador con un generador con semilla fija
(función `seededRandom`), para que los números sean consistentes entre recargas:

- **140 vehículos** en total: 8 Ingreso, 14 Preparación, 61 Publicado, 17 Reservado,
  40 Vendido.
- Catálogo de 12 modelos reales (Toyota Hilux SRV, Ford Ranger, Mazda CX-5, Honda CR-V,
  Kia Sportage, VW Tiguan, Hyundai Tucson, Renault Duster, Nissan Versa, Toyota Corolla,
  Chevrolet Sail, Suzuki Swift), con precios/márgenes/patentes/VIN generados de forma
  realista según tipo de carrocería.
- Las ventas (vista "Ventas y Cierre") y el ranking de vendedores del Dashboard se calculan
  a partir de los 40 vehículos vendidos (no son filas sueltas hardcodeadas).
- Fotos de vehículos: 6 fotos reales de stock de Unsplash (licencia libre, uso comercial
  permitido), mapeadas por tipo de carrocería (pickup, 3 variantes de SUV, 2 sedanes). No
  son el modelo/año exacto de cada ficha — es una limitación conocida y aceptada (ver
  `docs/DECISIONES.md`).

## Identidad visual

- Fondo oscuro premium (graphite/negro), glassmorphism (tarjetas con blur y bordes sutiles),
  tipografía Inter + JetBrains Mono (para cifras).
- Colores: acento azul `--accent` (#5b8cff), verde menta `--accent-2` (#7ee3c8), violeta
  `--accent-3` (#b98bff).
- Evitar: colores saturados, estética de concesionario tradicional, fotos genéricas de
  autos sin tratamiento visual consistente.
- Cada foto de vehículo se muestra con el mismo marco (`.veh-thumb` + `.veh-photo`):
  mismo tamaño, mismo recorte (`object-fit:cover`), mismo degradado de superposición —
  para que se sientan parte de una misma galería aunque sean fotos de distintos fotógrafos.

## Tecnologías

- HTML/CSS/JS plano, sin framework, sin build step. Fuentes vía Google Fonts CDN.
- Sin backend: todo el estado vive en arrays de JS en el cliente (no hay base de datos).
- Hosting: **Vercel**, importado directamente desde el repo de **GitHub**
  (`github.com/akirozzzz/motoros`), auto-deploy en cada push a `main`.
- Generación de documentos auxiliares (PRD) con la librería `docx` (Node), solo para crear
  el archivo `.docx`, no es parte del sitio.

## Decisiones ya aprobadas (resumen — detalle en `docs/DECISIONES.md`)

- Fotos reales de stock (Unsplash) en vez de fotos descargadas/generadas o ilustración
  genérica, con el mismo tratamiento visual en todas.
- Inventario se muestra como lista de fichas completas (no tabla comprimida), con Kanban de
  resumen arriba (máx. 4 tarjetas por columna + "+N más").
- Login previo a la plataforma, estética propia de MotorOS (no la de GoAuto, que no se pudo
  ver por estar detrás de JS sin navegador conectado).
- Widget flotante de MotorOS AI en toda la plataforma, con demo de restricción por rol
  (Dueño ve todo, Vendedor no ve datos financieros sensibles).
