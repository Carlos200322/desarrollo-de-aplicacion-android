# Registro de cambios

Todos los cambios relevantes de **CitaFácil** se documentan en este archivo.

El formato se basa en [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/) y el proyecto sigue el [versionado semántico](https://semver.org/lang/es/).

## [Próximo] — Módulos 6 a 8

### Planeado
- Completar el flujo de reserva con persistencia local en Room y sincronización con Firestore (módulo 6).
- Implementar el panel administrativo con indicadores de ocupación, ingresos y ausentismo (módulo 7).
- Publicar el código fuente y este README en GitHub Classroom antes del módulo 7.
- Programar los recordatorios a 24 h y 2 h con WorkManager y Firebase Cloud Messaging (módulo 8).
- Escribir pruebas unitarias del caso de uso de disponibilidad y pruebas de instrumentación de los DAO.
- Generar el Android App Bundle firmado y entregar el programa final (módulo 8).

## [0.3.0] — 2026-10-18 · Módulo 5 (versión actual)

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
