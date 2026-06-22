# Documento Funcional - Gestor de Filas Fertya

## 1. Descripción general

Este proyecto es un sistema web de gestión de filas para Fertya, publicado en Firebase Hosting como `https://gestor-fila.web.app`.

La aplicación consta de cuatro pantallas principales:
- `index.html`: puerta de acceso con los botones de navegación.
- `autorecepcion.html`: autogestión de llegada de pacientes con generación de código y QR.
- `recepcion.html`: panel de recepción para llamar, devolver y finalizar turnos.
- `pantalla.html`: pantalla de sala de espera que muestra los turnos en tiempo real.
- `seguimiento.html`: seguimiento de turno mediante token en URL.

## 2. Flujo funcional

### 2.1. Página principal (`index.html`)
- Muestra logo y cuatro opciones.
- Navega hacia:
  - `autorecepcion.html`
  - `recepcion.html`
  - `pantalla.html`
  - `seguimiento.html`

### 2.2. Autogestión de fila (`autorecepcion.html`)
- El paciente ingresa:
  - DNI
  - motivo de visita
- Al enviar el formulario:
  - se genera un token UUID.
  - se crea un turno en Firestore en la colección `turnos_fertya`.
  - se actualiza un contador atómico en colección `counters` según la letra del motivo.
  - se obtiene un código visible del tipo `C-001`, `L-002`, etc.
  - se muestra el código en pantalla y un QR.

#### Reglas de motivo -> letra
- `CON` / `C` -> `C`
- `LAB` / `L` -> `L`
- `ES` / `E` -> `E`
- `CT` / `T` / `TRAT` -> `T`
- `EP` / `P` / `PREV` -> `P`
- `OT` / `O` -> `O`
- default -> `C`

### 2.3. Panel de Recepción (`recepcion.html`)
- Permite seleccionar una recepción y ver una tabla con los turnos.
- Obtiene turnos en tiempo real de Firestore, ordenados por `createdAt`.
- Acciones disponibles:
  - `Llamar`: cambia `estado` a `llamado`, establece `horaLlamado`, `llamadoPor` y agrega evento de `llamadas`.
  - `Devolver`: cambia el turno de `llamado` a `en-fila`.
  - `Finalizar`: cambia el turno a `finalizado`.
- Botón `Cerrar jornada / Limpiar panel`:
  - al confirmar, finaliza en servidor todos los turnos `en-fila` del día actual.
  - si se elige limpiar la vista, no modifica la base de datos, solo oculta los datos locales.
- Botón `Exportar día (CSV)` genera reporte de turnos.

### 2.4. Pantalla de sala de espera (`pantalla.html`)
- Muestra turnos en tiempo real usando dos consultas Firestore:
  - `estado == 'en-fila'`
  - `estado == 'llamado'`
- Ordena y renderiza tarjetas de turno.
- Resalta los turnos con `estado === 'llamado'`.

### 2.5. Seguimiento de turno (`seguimiento.html`)
- Se abre con un parámetro `token` en la URL: `seguimiento.html?token=...`
- Busca el documento en `turnos_fertya` por el campo `token`.
- Si encuentra el turno:
  - consume el token (marca `tokenConsumed` y `tokenConsumedAt`).
  - muestra el estado real del turno.
  - reproducción opcional de sonido cuando el turno pasa a `llamado`.
- Si la página se abre localmente como `file://`, la aplicación muestra un mensaje de advertencia sobre la necesidad de servidor web.

## 3. Firebase y Firestore

### 3.1. Configuración Firebase
- `projectId`: `gestor-fila`
- `authDomain`: `gestor-fila.firebaseapp.com`
- `storageBucket`: `gestor-fila.firebasestorage.app`
- `apiKey` y `appId` incluidos en el código.
- Se usa autenticación anónima (`signInAnonymously`) para acceder a Firestore.

### 3.2. Colecciones principales

#### `turnos_fertya`
Campos usados:
- `dni`
- `motivoCodigo`
- `codigoVisible`
- `token`
- `estado` (`en-fila`, `llamado`, `finalizado`)
- `createdAt`
- `horaIngreso`
- `horaLlamado`
- `llamadoPor`
- `tokenConsumed`
- `tokenCreatedAt`
- `tokenConsumedAt`
- `tokenConsumedBy`
- `llamadas` (array de objetos con `by` y `at`)

#### `counters`
- Documentos con ID igual a la letra del servicio.
- Campo `seq` para mantener el conteo secuencial por categoría.

## 4. Estructura de archivos recuperados

- `public/index.html`
- `public/autorecepcion.html`
- `public/recepcion.html`
- `public/pantalla.html`
- `public/seguimiento.html`
- `public/styles.css`
- `public/img/logo-fertya.png`

## 5. Observaciones técnicas

- El proyecto usa Firebase JS SDK 12.6.0.
- Hay dependencias externas cargadas desde CDN:
  - `https://cdnjs.cloudflare.com/ajax/libs/qrious/4.0.2/qrious.min.js`
- La página `seguimiento.html` depende de recibir un token seguro en la URL.
- Las páginas deben servir desde un servidor HTTP local para que los módulos ES funcionen correctamente.

## 6. Cómo descargar el documento

Este archivo ya está creado en la carpeta raíz del proyecto:
- `c:\Users\yhummel\Documents\Gestor-Filas-Fertya\DOCUMENTO_FUNCIONAL.md`

Puedes descargarlo desde el explorador de archivos de VS Code o abrirlo y guardarlo con `Guardar como...`.

---

**Nota:** si deseas, también puedo generar una versión en PDF o en formato `.docx` para descargar más fácilmente.