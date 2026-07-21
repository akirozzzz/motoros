# CLAUDE.md — Reglas de trabajo para MotorOS

Este archivo es para cualquier asistente (Claude u otro) que trabaje en este repositorio.
Léelo completo antes de tocar código.

## Qué es este repo

MotorOS: landing + prototipo de producto para un DMS (Dealer Management System) con IA,
dirigido a automotoras. Es un proyecto **estático**, sin build step ni backend real:
todo es HTML/CSS/JS plano, autocontenido, pensado para desplegarse en Vercel directo desde GitHub.

Ver `docs/CONTEXTO.md` para el detalle de producto, y `docs/DECISIONES.md` para el porqué
de las decisiones ya tomadas. `docs/PENDIENTES.md` tiene el estado de trabajo actual.

## Antes de tocar código

1. Lee `CLAUDE.md` (este archivo), `docs/CONTEXTO.md` y `docs/PENDIENTES.md`.
2. Revisa `git log --oneline` para entender el historial reciente.
3. Si algo no está claro o el cambio es grande/estructural, pregunta antes de asumir.
4. No reemplaces archivos completos si un cambio quirúrgico (Edit puntual) alcanza.

## Reglas de trabajo (no negociables salvo que el usuario lo pida explícitamente)

- No borres funciones, secciones, textos, imágenes, variables ni dependencias sin preguntar.
- No reemplaces la web completa sin autorización explícita.
- Respeta el diseño y las decisiones ya aprobadas (ver `docs/DECISIONES.md`).
- Prioriza: mobile-first / responsive, velocidad, accesibilidad, claridad visual.
- Divide cambios grandes en etapas chicas y revisables, con explicación simple antes de cada una.
- Después de cada etapa, decir qué revisar en la vista previa de Vercel.
- **No hagas `git push` ni despliegues sin confirmación explícita del usuario en ese turno.**
  Deja los cambios comiteados localmente y listos; el usuario decide cuándo publicarlos.
- Actualiza `docs/PENDIENTES.md` (y `docs/DECISIONES.md` si aplica) al terminar cada tarea.

## Restricciones técnicas del entorno (importantes, no son elección de diseño)

- El sandbox donde corre Claude en esta sesión **no tiene acceso de red saliente** a
  github.com, vercel.com ni al registro de npm (bloqueado por allowlist). Esto significa:
  - Claude puede `git commit` localmente en la carpeta conectada, pero el `git push` lo debe
    correr el usuario desde su propia terminal (ya tiene `gh` instalado y autenticado).
  - No se pueden instalar paquetes npm nuevos sin conexión; usar lo que ya esté disponible
    globalmente (ej. `docx` para generar Word) o pedir ayuda al usuario si falta algo.
- Claude nunca debe escribir, pegar ni manejar contraseñas, tokens o API keys, ni siquiera si
  el usuario los pasa directamente en el chat. Si eso ocurre, hay que avisar que se traten como
  comprometidos y haya que revocarlos.
- No hay backend real: todo el "estado" (inventario, ventas, leads) vive en arrays de JS
  generados en el cliente. No confundir esto con una base de datos real.

## Estructura del repo

```
index.html                → Landing pública de marketing
app.html                  → Prototipo del producto (DMS interno)
login.html                → Pantalla de login (demo, sin backend real)
README.md                 → Instrucciones de uso/deploy para humanos
CLAUDE.md                 → Este archivo
docs/
  CONTEXTO.md              → Qué es MotorOS, usuarios, features, stack, diseño
  PENDIENTES.md            → Tareas pendientes, trabajo en curso, bugs conocidos
  DECISIONES.md            → Decisiones importantes y su motivo
  estrategia-y-copy.md     → Copy de marca y narrativa (histórico, de las primeras versiones)
  motoros-prd.docx         → PRD original del producto completo (12 módulos)
```

## Convenciones de código

- Cada archivo HTML es autocontenido: CSS inline en `<style>`, JS inline en `<script>`, sin
  build step. Las fuentes se cargan de Google Fonts vía CDN.
- Paleta y estilo visual: fondo oscuro premium (graphite/negro), glassmorphism, acentos en
  azul (`--accent`), verde menta (`--accent-2`) y violeta (`--accent-3`). No usar colores
  saturados ni estética de "concesionario tradicional".
- Las fotos de vehículos son fotos reales de stock (Unsplash, licencia libre para uso
  comercial) enlazadas por URL — no se descargan al repo. Ver `docs/DECISIONES.md` para el
  porqué (no hay forma de descargar/generar imágenes en el sandbox, y las fotos de Google
  Images tienen problemas de copyright).
- Todos los íconos de la interfaz son **Lucide Icons**, cargados vía CDN
  (`unpkg.com/lucide@latest`) y convertidos con `<i data-lucide="nombre">` +
  `lucide.createIcons()`. **No usar emojis ni otros packs de íconos.** Si agregas contenido
  nuevo con íconos dentro de una función que se re-ejecuta después de la carga inicial
  (chat, filtros, CRM, etc.), hay que llamar `lucide.createIcons()` de nuevo al final de esa
  función — si no, el ícono nuevo no se convierte a SVG y queda invisible.
- Los datos de inventario/ventas en `app.html` se generan con un PRNG con semilla fija
  (`seededRandom`), así los números son consistentes entre recargas de la página pero no
  son datos reales de ningún cliente.
- Commits: mensajes en español, con resumen en la primera línea y bullets del detalle debajo.

## Cómo verificar antes de dar algo por terminado

- Balance de tags HTML + `node --check` sobre el contenido de cada `<script>` antes de
  commitear (evita romper la página con un cambio mal cerrado).
- Si se agregan/cambian `id`/`class` referenciados desde JS, verificar con grep cruzado que
  coincidan entre el HTML y el JS.
- No asumir que una URL de imagen "debería" funcionar — si hay forma de verificarla
  (`web_fetch`, o pedirle al usuario que mire la vista previa), hacerlo.
