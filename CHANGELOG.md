# Registro de cambios

Todos los cambios relevantes de **CitaFácil** se documentan en este archivo.

El formato se basa en [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/) y el proyecto sigue el [versionado semántico](https://semver.org/lang/es/).

## [Próximo] — Después del término académico

### Planeado
- Migrar a Kotlin con Room y Cloud Firestore para compartir la agenda entre el cliente y el negocio.
- Enviar recordatorios desde el servidor con notificaciones push y una pasarela de SMS.
- Panel web del administrador con indicadores de ocupación, ingresos y ausentismo.
- Piloto con negocios reales y publicación en Google Play.

## [1.0.0] — 2026-10-18 · Proyecto final (versión actual)

### Agregado
- App funcional en MIT App Inventor con las secciones Reservar, Mis citas e Indicadores.
- Persistencia con TinyDB, detección de doble reserva y cancelación con diálogo de confirmación.
- Recordatorio con temporizador y sonido, confirmación por SMS y ruta a Google Maps con la ubicación del usuario.
- Gráfica de citas por servicio en un lienzo que responde al toque.
- `app/CitaFacil.apk`, `app/CitaFacil.aia`, código QR permanente e informe final en `docs/`.

### Cambiado
- El entregable del término es un prototipo en App Inventor; la arquitectura con Kotlin y Firebase pasa a la hoja de ruta.
- README actualizado a la versión 1.0.0.

## [0.3.0] — 2026-10-18 · Módulo 5

### Agregado
- Sección de arquitectura por capas (interfaz, dominio y datos).
- Tabla de correspondencia entre los esquemas de pantalla (wireframes) y las entidades.
- Modelo entidad-relación normalizado a 3FN con siete entidades, incluidas `ProfesionalServicio` (N:M) y `CitaHistorial` (auditoría).
- Estrategia de prevención de doble reserva mediante detección de traslapes dentro de una transacción.
- Entidad `CitaEntity` y `CitaDao` en Room.
- Este archivo `CHANGELOG.md`.

### Cambiado
- Room pasa de caché a **fuente única de verdad** para la interfaz.
- Precios almacenados como enteros en centavos y fechas en UTC.
- README reorganizado y actualizado a la versión 0.3.

### Corregido
- Referencias en formato APA 7 con cursivas en los títulos.

## [0.2.0] — Módulos 3 y 4

### Agregado
- Proyecto base en Android Studio con Kotlin, Jetpack Compose y Material 3.
- Autenticación con Firebase Authentication por correo y teléfono.
- Primera versión del modelo de datos y navegación según el rol (cliente o administrador).

## [0.1.0] — 2026-08-31 · Módulo 1

### Agregado
- Borrador inicial: descripción, problema, plataforma, interfaces, requisitos, tres wireframes y cronograma.
- Repositorio y wiki del proyecto en GitHub.
