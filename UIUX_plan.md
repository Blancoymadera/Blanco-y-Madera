# Revisión integral de UI/UX

## 1) Auditoría de problemas actuales
- **Estructura y semántica**: la vista principal agrupa filtros, carrito y reportes en un layout de dos columnas, pero carece de zonas claramente delimitadas para navegación y pasos de flujo (no hay barra de progreso ni breadcrumbs para compra o cotización). Los formularios están construidos con `div` y `input` sin `<form>` ni mensajes de error/validación en vivo, lo que dificulta accesibilidad y envío con teclado.
- **Accesibilidad**: solo el buscador tiene `aria-label`; el resto de campos no expone `aria-describedby` ni estados de error; no se define foco visible consistente y algunos botones usan solo color para jerarquía. No hay skip-link ni anuncio de cambios dinámicos en listados, toasts o loaders.
- **Consistencia visual**: se mezclan pesos de tipografía y esquinas (rounded-lg/xl) sin sistema de espaciados; la paleta se centra en ámbar y piedra pero sin tokens definidos para hover, bordes ni fondos neutrales. Diferentes sombreados/gradientes compiten con jerarquía de contenido.
- **Flujos clave**: búsqueda y filtrado están presentes pero sin guardado de filtros ni resultados vacíos claros; el checkout/cotización carece de pasos guiados (datos cliente, método de pago, confirmación) y no hay resumen en mobile (el carrito completo se oculta tras un botón). No existen estados vacíos específicos para pendientes o reportes, ni confirmaciones accesibles de acciones críticas.
- **Feedback y estados**: hay loader superpuesto en catálogo pero no en acciones de carrito o reportes; los toasts no son anunciados vía `aria-live` y podrían tapar contenido en mobile. Los estados de error/éxito no se muestran junto a los campos.
- **Responsive**: se usa un grid 2x1; en mobile el carrito pasa a vista separada pero no hay navegación fija para volver ni botones grandes para acción primaria. El alto fijo de listas puede provocar scroll interno sin control de foco.

## 2) Plan de cambios priorizados
1. **Fundaciones de diseño** (tokens de color, tipografía, espaciado, sombras, radios) + set de focos accesibles. *Esfuerzo*: 1-2 días.
2. **Componentes base accesibles**: botones, inputs/selects, toasts `aria-live`, banners de error, modales con trampa de foco, tabs/steps. *Esfuerzo*: 2-3 días.
3. **Flujos clave simplificados**: registro/login (si aplica), cotización/checkout en 3 pasos (datos, envío/pago, confirmación), búsqueda con filtros guardables y estados vacíos. *Esfuerzo*: 3-5 días.
4. **Tablas/listas mejoradas**: cabeceras pegajosas, acciones in-row, estados vacíos/errores/carga, control de densidad. *Esfuerzo*: 2 días.
5. **QA y accesibilidad**: validación de contraste, navegación con teclado, Lighthouse A11y/Best Practices, QA en breakpoints (360px, 768px, 1024px). *Esfuerzo*: 1-2 días.

## 3) Tokens de diseño propuestos
- **Colores (WCAG AA sobre fondo claro)**
  - Primario: `#b7791f` (Amber 700) / hover `#9a670f`; texto sobre primario `#fff`.
  - Secundario neutro: `#1f2937` (Gray 800) / hover `#111827` para acciones secundarias.
  - Fondo neutro: `#f8fafc`; superficies: `#ffffff`, `#f1f5f9`.
  - Bordes/dividers: `#e2e8f0`; focus ring: `#2563eb` (Blue 600) con `outline-offset: 2px`.
  - Estados: éxito `#15803d`, warning `#c2410c`, error `#b91c1c`, info `#0f172a`.
- **Tipografía**: `"Inter", sans-serif`, escala 12/14/16/18/20/24/32/40px; pesos 400/500/600/700; altura de línea 1.4.
- **Espaciado**: escala 4/8/12/16/20/24/32/40px; gutters de layout 16-24px; radios 8px (base) y 12px (cards); sombras `sm: 0 1px 3px rgba(0,0,0,0.08)`, `md: 0 4px 12px rgba(0,0,0,0.08)`.
- **Iconografía**: set lineal de 24px, con tamaño mínimo 20px y relleno de 12-16px en botones icon-only.

## 4) Guidelines de componentes
- **Botón**: variantes primario/secondary/ghost/destructive; alto 44px; foco visible (`ring-2` azul), estado loading con spinner y aria-busy; deshabilitado con `aria-disabled` y contraste 3:1.
- **Input/select**: label siempre visible y asociado; helper/error text con `id` para `aria-describedby`; validación en vivo mostrando mensajes en rojo y borde `error`; placeholder no reemplaza label.
- **Checkbox/Radio**: áreas clicables amplias, soporte de teclado (espacio/enter), grupos con `fieldset/legend`.
- **Tabs/Steps**: navegables con flechas y `role="tablist"`; steps numerados para checkout/cotización (Datos → Envío/pago → Confirmación).
- **Modal/Drawer**: foco inicial en título o primer campo, trampa de foco, cierre con Esc y botón visible; overlay semitransparente.
- **Toast**: contenedor con `role="status"`/`aria-live="polite"`, botón de cerrar, ubicación superior derecha en desktop e inferior en mobile.
- **Tablas/Listas**: cabecera sticky, filas con hover suave, acciones inline; estados vacíos con icono y CTA; errores con retry; carga con skeleton.
- **Estados vacíos/errores/carga**: patrones estándar con mensaje breve, explicación secundaria y acción.

## 5) Wireframes/mocks de pantallas clave (texto)
- **Home/Catálogo (desktop)**: barra superior con logo + búsqueda global + CTA "Nueva cotización"; layout 3 columnas (filtros estrechos, grid de tarjetas, resumen de carrito pegajoso a la derecha). Tarjetas con foto, nombre, precio, badges de stock y botón "Agregar".
- **Home/Catálogo (mobile)**: header compacto con búsqueda; filtros en sheet deslizable; cards en 1 columna; botón flotante para abrir carrito/resumen; toast no intrusivo arriba.
- **Login/Registro**: panel centrado, 1 columna, inputs con labels, enlace recuperar contraseña, botones sociales opcionales; checklist de contraseña y validación en vivo.
- **Checkout/Cotización**: wizard de 3 pasos; paso 1 datos de cliente (nombre, teléfono, email), paso 2 dirección/pago, paso 3 resumen + confirmar. Barra de progreso y botón back en mobile.
- **Búsqueda avanzada**: filtros chips (categoría, rango de precio, disponibilidad), posibilidad de guardar filtro; resultados con estados vacío y paginación simple.

## 6) Lista de mejoras priorizadas con estimaciones
- Implementar tokens (color, tipografía, spacing) en un archivo de estilos central y aplicar a layout principal. *1-2 días*.
- Refactorizar filtros/búsqueda en un formulario accesible con `aria-live` para resultados y estado vacío. *1-2 días*.
- Crear componentes base (Button/Input/Select/Checkbox/Toast/Modal/Stepper) con variantes accesibles. *3 días*.
- Rediseñar carrito/cotización en sidebar pegajoso (desktop) y drawer (mobile) con pasos y validación en vivo. *2-3 días*.
- Añadir estados vacíos/carga/errores a catálogo, pendientes y reportes, con retry y skeletons. *1-2 días*.
- Ajustar responsive (breakpoints 360/768/1024) con pruebas de teclado y foco. *1 día*.
- QA final: Lighthouse A11y/Best Practices, contraste (axe/Pa11y), checklist WCAG AA y accesibilidad de teclado. *1 día*.

## 7) Checklist de accesibilidad y pruebas sugeridas
- Contraste AA (4.5:1 texto normal, 3:1 texto grande) en texto, iconos y botones; test con axe/Pa11y.
- Foco visible consistente en todos los elementos interactivos; orden lógico de tabulación y skip-link.
- `aria-live` en toasts/listas dinámicas; `role="status"` en loaders; `aria-expanded`/`aria-controls` en menús y acordeones.
- Formularios con labels asociados, `aria-invalid`, `aria-describedby` para errores y helper text; validación en vivo sin bloquear teclado.
- Navegación con teclado en modales, tabs, selects personalizados y listas con scroll; evitar scroll-trap oculto.
- Revisión Lighthouse (A11y/Best Practices), pruebas manuales en mobile/tablet/desktop, y QA con lector de pantalla (NVDA/VoiceOver) en los flujos clave.
