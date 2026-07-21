# MotorOS — Guía rápida de identidad

## El símbolo

La "M" está construida como tres líneas de movimiento (velocidad) que se transforman en un
trazo sólido y estable — la idea: información dispersa y en movimiento que se convierte en
un sistema ordenado. Es 100% vectorial (paths SVG), no hay ninguna imagen rasterizada.

## Archivos incluidos

| Archivo | Uso |
|---|---|
| `motoros-icon.svg` | Símbolo solo, con gradiente, fondo transparente |
| `motoros-icon-mono-white.svg` / `-mono-black.svg` | Símbolo solo, un color |
| `icon-square.svg` + `favicons/icon-*.png` (16/32/48/180/192/512px) | Ícono cuadrado con fondo, para favicon / ícono de app |
| `motoros-logo-horizontal-dark.svg` | Isologo horizontal (símbolo + "MotorOS"), fondo oscuro — **para el header del sitio** |
| `motoros-logo-horizontal-light.svg` | Igual, adaptado a fondo claro |
| `motoros-logo-horizontal-transparent.svg` | Igual, sin fondo (para superponer sobre cualquier color oscuro) |
| `motoros-logo-stacked-dark.svg` | Lockup vertical completo: símbolo + wordmark + tagline, fondo oscuro — **para portada / hero / presentaciones** |
| `motoros-logo-stacked-transparent.svg` | Igual, sin fondo |
| `motoros-logo-mono-white.svg` / `-mono-black.svg` | Isologo horizontal en un solo color, para fondos fotográficos, sellos, grabado, impresión a un color |

## Colores

| Uso | Hex |
|---|---|
| Azul claro (inicio del gradiente) | `#8FC7FF` |
| Azul medio (gradiente) | `#3B82F6` |
| Azul profundo (fin del gradiente) | `#1D4ED8` |
| Fondo oscuro de marca | `#060A12` |
| Texto oscuro (sobre fondo claro) | `#0B1220` |
| Azul del tagline | `#6FA3FF` |

El símbolo y la palabra "OS" siempre usan el gradiente diagonal (0%→100%, esquina superior
izquierda a inferior derecha) entre estos tres azules. La palabra "Motor" es blanca sobre
fondo oscuro, o `#0B1220` sobre fondo claro.

## Tipografía

- **Wordmark ("MotorOS"):** Poppins Bold Italic (o Montserrat/Inter Bold Italic como
  alternativa si no está disponible Poppins).
- **Tagline:** Poppins SemiBold, mayúsculas, letter-spacing amplio (`+3` a `+4`).
- Si se usa en el sitio web (HTML/CSS), Poppins se puede cargar gratis desde Google Fonts.

## Espaciado y tamaño mínimo

- Deja un margen libre alrededor del logo de al menos el ancho de la "M" del símbolo (zona
  de resguardo).
- Tamaño mínimo recomendado: 24px de alto para el isologo horizontal, 32px para el símbolo
  solo (por debajo de eso el detalle de las líneas de movimiento se pierde).

## Qué no hacer

- No estirar ni deformar el símbolo de forma no proporcional.
- No separar el gradiente en colores planos fuera de las versiones monocromáticas ya
  provistas.
- No poner la versión "dark" (texto blanco) sobre fondos claros, ni la "light" (texto oscuro)
  sobre fondos oscuros — usar siempre la variante monocromática o transparente correcta
  según el fondo.
- No usar una fuente distinta a la especificada para el wordmark; el itálico es parte de la
  identidad (transmite velocidad).
