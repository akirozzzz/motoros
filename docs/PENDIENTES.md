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

## Próximos pasos sugeridos (sin confirmar con el usuario todavía — ver primero con él)

En orden de impacto probable:

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

## Cómo publicar cambios (recordatorio para el asistente)

Los cambios quedan comiteados localmente en `~/CLAUDE/motoros` pero **no se hace push
automático**. El usuario corre esto en su propia terminal cuando quiera publicar:

```
cd ~/CLAUDE/motoros
git push
```

Ya tiene `gh` (GitHub CLI) instalado y autenticado en su máquina, así que el push no debería
pedir usuario/token de nuevo salvo que expire la sesión.
