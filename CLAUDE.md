# Gastos — contexto del proyecto

## Qué es
App personal de gastos e ingresos para mi iPhone, gratuita, que sustituye mi Excel. Objetivo principal: **registrar un gasto en menos de 5 segundos**. Soy el único usuario. No tengo formación en programación: explica los cambios en lenguaje sencillo y evita jerga innecesaria.

## Arquitectura (coste cero)
- **Frontend:** PWA de un solo archivo ([index.html](index.html)), con [sw.js](sw.js) (service worker, deja la app disponible sin conexión) y [manifest.webmanifest](manifest.webmanifest). Más los iconos (icon-192, icon-512, apple-touch-icon). Publicada con GitHub Pages desde la rama `main`, carpeta raíz: https://santiagofloressolbes.github.io/gastos/
- **Base de datos:** un Google Sheet con las pestañas Movimientos, Deuda, Personas, Categorías y Notas de importación.
- **Puente:** un Google Apps Script (`Code.gs`, **no está en este repo**) desplegado como aplicación web. La app le hace POST con cuerpo `text/plain` (evita el preflight de CORS) y una clave compartida. Acciones: `ping`, `data`, `add`, `delete`.
  - Los cambios en Code.gs **no se despliegan solos**: hay que copiarlos al editor de Apps Script y crear una **"Nueva versión"** de la implementación existente (no una implementación nueva, para no cambiar la URL).
- **Offline:** cola local en localStorage (claves `cfg`, `db`, `queue`, `delq`, `tab`), interfaz optimista, deshacer de 5,5 s, sincronización al volver la conexión.

## Seguridad (crítico)
- El repositorio es **público**. NUNCA subir la URL de la implementación de Apps Script, la clave, ni datos reales (movimientos, Excel, deudas).
- En Code.gs la clave debe quedar siempre como `'CAMBIA-ESTA-CLAVE'`.
- La URL y la clave se guardan solo en el localStorage del iPhone (se introducen en Ajustes).
- La app acepta la URL y la clave en la dirección (`#u=...&k=...`) y las guarda. Tras leerlas, debe borrar el hash con `history.replaceState` para que la clave no quede en el historial ni en enlaces copiados. Nunca guardar ni pegar un enlace con ese hash en el repo, commits o chats.

## Convenciones de producto
- Español de España/México, nunca argentino.
- Moneda: euros. Ámbitos: Personal, Sporture, LaLiga. Tipos: Gasto, Ingreso.
- Las personas ("con quién") son opcionales. Categoría y personas se sugieren a partir del historial.
- Diseño: tinta pino `#10302B`, fondo niebla `#E7EEEB`, acento azafrán `#F2B705`, barras `#2F7F70`, cifras con `ui-rounded`, modo oscuro soportado.
- Prioridad de UX: pocos toques, teclado numérico propio, nada que estorbe al registrar.

## Flujo de trabajo
- Antes de dar un cambio por terminado, probarlo. Las pruebas con Playwright están en mi entorno de Claude, no en este repo; si hace falta, abrir la app en un servidor local y comprobar el flujo.
- Cada cambio en index.html o sw.js: **subir `CACHE` en sw.js** (ahora `gastos-v3`; luego v4…) para que el iPhone cargue la versión nueva.
- Commits en español, mensajes cortos. **Commit y push a `main` solo cuando yo lo confirme.**
- Para cambios grandes, proponer un plan corto antes de tocar código.

## Estado actual
Registro de gasto/ingreso, sugerencia de categoría, personas opcionales, editar y eliminar movimientos, resumen con porcentajes por categoría y tendencia de 12 meses, ranking "con quién", módulo de deuda con familiares (saldo y pagos, sin intereses ni plazo).

## Backlog próximo (orden aproximado de prioridad)
1. Registrar pagos de la deuda con familiares.
2. Simulador de plan de pagos con colchón de seguridad (cuando tenga ingresos estables de LaLiga).
3. Presupuestos por categoría.
4. Gastos recurrentes.
5. Atajo de Siri / widget.
6. Recordatorio diario.
7. Importar varios movimientos de una vez (pegando texto o desde captura del banco).
