# CitaFácil — Gestión de citas y recordatorios

![Versión](https://img.shields.io/badge/versión-1.0.0-blue) ![Plataforma](https://img.shields.io/badge/plataforma-Android-green) ![Kotlin](https://img.shields.io/badge/lenguaje-Kotlin-purple) ![Estado](https://img.shields.io/badge/estado-prototipo%20funcional-brightgreen)

Aplicación nativa para Android que permite a barberías, salones de belleza y clínicas ambulatorias pequeñas digitalizar su agenda. El cliente reserva sin llamar por teléfono y recibe recordatorios automáticos; el administrador gestiona horarios, servicios y personal, y consulta indicadores de ocupación y ausentismo.

> **Curso:** Desarrollo de Aplicaciones Android · **Autor:** Carlos · **Entrega actual:** Proyecto final (versión 1.0.0, 18 de octubre de 2026)

## Contenido

1. [Descargar la app](#descargar-la-app)
2. [Problema](#problema)
3. [Plataforma y tecnologías](#plataforma-y-tecnologías)
4. [Funcionalidad](#funcionalidad)
5. [Arquitectura](#arquitectura)
6. [Diseño de la base de datos](#diseño-de-la-base-de-datos)
7. [App en MIT App Inventor](#app-en-mit-app-inventor)
8. [Wireframes](#wireframes)
9. [Cronograma](#cronograma)
10. [Registro de cambios](#registro-de-cambios)
11. [Cómo ejecutar el proyecto](#cómo-ejecutar-el-proyecto)
12. [Reflexión](#reflexión)
13. [Referencias](#referencias)

## Descargar la app

| Archivo | Descripción |
|---|---|
| [`app/CitaFacil.apk`](app/CitaFacil.apk) | Aplicación instalable para Android |
| [`app/CitaFacil.aia`](app/CitaFacil.aia) | Proyecto fuente de MIT App Inventor (archivo .zip) |
| [`app/CitaFacil_QR.png`](app/CitaFacil_QR.png) | Código QR para descargar el .apk desde el teléfono |
| [`docs/CitaFacil_Informe_Final.docx`](docs/CitaFacil_Informe_Final.docx) | Informe final del proyecto |

<img src="app/CitaFacil_QR.png" alt="Código QR de descarga de CitaFacil.apk" width="200">

Para instalarla, escanea el código QR con el teléfono, descarga el archivo y permite la instalación de aplicaciones de origen desconocido cuando Android lo pida.

## Problema

Los negocios de servicios por cita pierden ingresos por tres causas:

- **Tiempo administrativo:** reservar por teléfono interrumpe el servicio en curso, y la agenda en papel no evita la doble reserva.
- **Ausentismo sin aviso:** cada hora vacía es una pérdida irrecuperable. Los recordatorios automatizados reducen de forma significativa la inasistencia (McLean et al., 2016).
- **Falta de datos:** sin registros digitales, las decisiones sobre horarios, personal o promociones se toman por intuición.

## Plataforma y tecnologías

Nivel de API objetivo **37 (Android 17)**; nivel mínimo **26 (Android 8.0)**.

| Capa | Tecnología | Función |
|---|---|---|
| Interfaz | Jetpack Compose, Material 3 | Pantallas declarativas |
| Arquitectura | MVVM, ViewModel, StateFlow | Separación de lógica y presentación |
| Persistencia local | Room | Fuente única de verdad y uso sin conexión |
| Backend en la nube | Cloud Firestore | Sincronización de la agenda en tiempo real |
| Autenticación | Firebase Authentication | Registro por teléfono y correo |
| Notificaciones | Firebase Cloud Messaging, WorkManager | Recordatorios programados |
| Construcción | Gradle | Dependencias y compilación |
| Control de versiones | Git y GitHub | Historial, ramas e integración continua |

## Funcionalidad

**Cliente:** registro, selección de servicio, profesional, fecha y hora; confirmación; historial; cancelación o reprogramación; recordatorios a 24 h y 2 h.

**Administrador:** agenda diaria con estados por color, registro manual de citas, catálogo de servicios, horarios y bloqueos, gestión del personal, indicadores y avisos puntuales.

<details>
<summary>Requisitos funcionales y no funcionales</summary>

1. Registro y autenticación con roles de cliente y administrador.
2. Mostrar solo horarios disponibles según la duración del servicio y la agenda del profesional.
3. Impedir la doble reserva mediante una transacción atómica.
4. Enviar recordatorios automáticos antes de la cita.
5. Cancelar y reprogramar dentro de una ventana configurable.
6. Conservar el historial de citas de cada cliente.
7. CRUD de servicios, horarios y personal para el administrador.
8. Indicadores de ocupación, ingresos y ausentismo por período.
9. Consulta de las citas próximas sin conexión.

**No funcionales:** respuesta en menos de 2 s; reserva en 4 pasos como máximo; accesibilidad según Material Design; datos cifrados en tránsito y reglas de seguridad en Firestore; arquitectura por capas con pruebas unitarias.
</details>

## Arquitectura

Se siguen los principios de la guía oficial de Google: separación de responsabilidades, interfaz controlada por modelos de datos, fuente única de verdad y flujo de datos unidireccional (Google, 2026c).

```mermaid
flowchart LR
    UI["Interfaz<br/>Compose + ViewModel"] -->|eventos| DOM["Dominio<br/>Casos de uso"]
    DOM --> REPO["Datos<br/>Repositorios"]
    REPO --> ROOM[("Room<br/>fuente de verdad")]
    REPO <--> FS[("Cloud Firestore")]
    ROOM -->|StateFlow| UI
```

| Capa | Componentes | Responsabilidad |
|---|---|---|
| Interfaz | `InicioScreen`, `ReservaScreen`, `AgendaAdminScreen` y sus ViewModel | Mostrar el estado y enviar eventos |
| Dominio | `ObtenerHorariosDisponibles`, `ReservarCita`, `CalcularIndicadores` | Reglas de negocio |
| Datos | `CitaRepository`, `ServicioRepository`, DAO de Room, cliente de Firestore | Persistencia y sincronización |

Las pantallas leen **solo** de Room; un proceso de sincronización escribe en Room los cambios que llegan de Firestore. Así la app funciona sin conexión y muestra siempre datos coherentes.

## Diseño de la base de datos

Modelo normalizado a tercera forma normal (Codd, 1970; Elmasri y Navathe, 2016).

```mermaid
erDiagram
    USUARIO ||--o{ CITA : reserva
    USUARIO ||--o| PROFESIONAL : es
    PROFESIONAL ||--o{ HORARIO : tiene
    PROFESIONAL ||--o{ CITA : atiende
    PROFESIONAL ||--o{ PROFESIONAL_SERVICIO : ofrece
    SERVICIO ||--o{ PROFESIONAL_SERVICIO : "ofrecido por"
    SERVICIO ||--o{ CITA : incluye
    CITA ||--o{ CITA_HISTORIAL : registra
    USUARIO {
        string id PK
        string nombre
        string telefono
        string correo
        string rol
    }
    PROFESIONAL {
        string id PK
        string usuario_id FK
        bool activo
    }
    SERVICIO {
        string id PK
        string nombre
        int duracion_min
        int precio_centavos
        bool activo
    }
    PROFESIONAL_SERVICIO {
        string profesional_id FK
        string servicio_id FK
    }
    HORARIO {
        string id PK
        string profesional_id FK
        int dia_semana
        string hora_inicio
        string hora_fin
    }
    CITA {
        string id PK
        string cliente_id FK
        string profesional_id FK
        string servicio_id FK
        long inicio
        long fin
        string estado
        long actualizado_en
    }
    CITA_HISTORIAL {
        string id PK
        string cita_id FK
        string estado_anterior
        string estado_nuevo
        string usuario_id
        long fecha
    }
```

**Decisiones de diseño**

- Precios como enteros en centavos para evitar errores de redondeo.
- Fechas en milisegundos UTC; la conversión a hora local ocurre solo en la interfaz.
- Borrado lógico (`activo`) de servicios y profesionales para conservar la integridad de las citas históricas.
- Índices compuestos `(profesional_id, inicio)` y `(cliente_id, inicio)` para las dos consultas más frecuentes.
- `CITA_HISTORIAL` como bitácora de auditoría: permite reconstruir quién cambió una cita y cuándo.

**Prevención de la doble reserva.** Dos citas chocan cuando el inicio de cada una es anterior al fin de la otra. `ReservarCita` verifica esa condición y crea la cita en una misma transacción: una transacción de Firestore en el servidor (Google, 2026e) y un método `@Transaction` en Room en el dispositivo (Google, 2026d).

```kotlin
@Entity(
    tableName = "cita",
    foreignKeys = [ForeignKey(entity = ProfesionalEntity::class,
        parentColumns = ["id"], childColumns = ["profesional_id"])],
    indices = [Index("profesional_id", "inicio"), Index("cliente_id", "inicio")]
)
data class CitaEntity(
    @PrimaryKey val id: String,
    @ColumnInfo(name = "cliente_id") val clienteId: String,
    @ColumnInfo(name = "profesional_id") val profesionalId: String,
    @ColumnInfo(name = "servicio_id") val servicioId: String,
    val inicio: Long,          // milisegundos UTC
    val fin: Long,
    val estado: EstadoCita,    // CONFIRMADA, EN_CURSO, COMPLETADA, CANCELADA, AUSENCIA
    @ColumnInfo(name = "actualizado_en") val actualizadoEn: Long
)

@Dao
interface CitaDao {
    @Query("""SELECT COUNT(*) FROM cita WHERE profesional_id = :prof
              AND estado != 'CANCELADA' AND inicio < :fin AND fin > :inicio""")
    suspend fun contarTraslapes(prof: String, inicio: Long, fin: Long): Int

    @Query("""SELECT * FROM cita WHERE cliente_id = :cliente AND inicio >= :ahora
              ORDER BY inicio LIMIT 1""")
    fun proximaCita(cliente: String, ahora: Long): Flow<CitaEntity?>
}
```

### Del wireframe a la base de datos

| Elemento del wireframe | Entidad y atributos | Operación |
|---|---|---|
| Tarjeta «Próxima cita» | Cita, Servicio, Profesional | Cita futura más cercana del cliente |
| Lista de servicios | Servicio (nombre, duración, precio) | Catálogo activo |
| Botones de hora | Horario y citas existentes | Franjas libres y traslapes |
| Ocupación y ausencias | Cita (estado, inicio, fin) | Agregación por fecha |
| Botón `[+]` en franja libre | Cita registrada por el administrador | Inserción manual |

## App en MIT App Inventor

El prototipo entregable se construyó en MIT App Inventor. Ocupa una pantalla con tres secciones que se alternan con pestañas: **Reservar**, **Mis citas** e **Indicadores**. La versión comercial con Kotlin y Firebase descrita arriba es la siguiente etapa.

| Tipo | Componentes |
|---|---|
| Botones | Pestañas, `BtnReservar`, `BtnCancelar`, `BtnSms`, `BtnComoLlegar` |
| Imágenes | `ImgEncabezado` (logotipo y lema) |
| Lienzo | `CnvGrafica`: gráfica de citas por servicio que responde al toque |
| Selectores | `ListPicker` de servicio y profesional, `DatePicker`, `TimePicker`, `TextBox` |
| Invisibles | `TinyDB` (base de datos), `Sound` (aviso), `Clock` (formato y temporizador), `LocationSensor`, `Notifier`, `Texting` (SMS), `ActivityStarter` (Google Maps) |
| Listas | Cada cita es una lista de 6 elementos; todas se guardan en la variable global `citas` |

**Bloques destacados**

- Variables globales: `citas`, `servicios`, `conteos`, `servicioSel`, `profesionalSel`, `latitud`, `longitud`, `avisadas`, `destino`.
- Procedimientos: `mostrarSeccion(seccion)`, `actualizarLista`, `guardarCitas`, `dibujarGrafica`.
- Eventos con argumentos: `CnvGrafica.Touched(x, y)`, `LocationSensor1.LocationChanged(latitude, longitude)`, `Notifier1.AfterChoosing(choice)`.
- `BtnReservar.Click` construye el instante con `Clock1.MakeInstantFromParts` y rechaza la reserva si el profesional ya tiene una cita a esa hora.
- `Clock1.Timer` revisa cada minuto si alguna cita empieza en menos de dos horas y muestra el recordatorio con sonido una sola vez.

## Wireframes

<table><tr><td>

**Inicio del cliente**
```
+-------------------------------+
| CitaFácil            [perfil] |
+-------------------------------+
|  Hola, Carlos                 |
|  PRÓXIMA CITA                 |
|  +-------------------------+  |
|  | Corte de cabello        |  |
|  | Vie 12 sep · 10:30 a.m. |  |
|  | Con: Luis               |  |
|  | [Reprogramar][Cancelar] |  |
|  +-------------------------+  |
|  [  RESERVAR NUEVA CITA  ]    |
|  Historial                 >  |
+-------------------------------+
| [Inicio]  [Citas]  [Ajustes]  |
+-------------------------------+
```
</td><td>

**Reserva**
```
+-------------------------------+
| <  Reservar cita              |
+-------------------------------+
| 1. Servicio                   |
| ( ) Corte        30 min  $10  |
| (•) Corte+barba  45 min  $15  |
| ( ) Afeitado     20 min   $8  |
| 2. Profesional  [ Luis   v ]  |
| 3. Fecha                      |
|  L  M  M  J  V  S  D          |
|  8  9 10 11 [12] 13 14        |
| 4. Hora                       |
| [09:00] [09:45] [-10:30-]     |
| [11:15] [12:00] [-12:45-]     |
|      [   CONTINUAR   ]        |
+-------------------------------+
```
</td><td>

**Agenda del administrador**
```
+--------------------------------+
| Agenda       < 12 sep 2026 >   |
| Ocupación: 78%   Ausencias: 1  |
+--------------------------------+
| 09:00 | Ana R.    | Corte    o |
| 09:45 | LIBRE            [ + ] |
| 10:30 | Carlos M. | Corte+B  o |
| 11:15 | Jorge P.  | Afeitado * |
| 12:00 | LIBRE            [ + ] |
| 12:45 | Sara L.   | Corte    x |
+--------------------------------+
| o Confirmada * En curso x Falta|
| [Agenda] [Servicios] [Reportes]|
+--------------------------------+
```
</td></tr></table>

## Cronograma

| Módulo | Entregable | Estado |
|---|---|---|
| 1 y 2 | Borrador del proyecto, repositorio y wiki | ✅ Completado |
| 3 y 4 | Autenticación, modelo de datos y navegación | ✅ Completado |
| 5 y 6 | Arquitectura, base de datos, flujo de reserva y persistencia local | ✅ Completado |
| 7 | Indicadores y publicación del código | ✅ Completado |
| 8 | Recordatorios, pruebas, .apk, QR e informe final | ✅ Completado |

## Registro de cambios

El historial completo está en [CHANGELOG.md](CHANGELOG.md). Resumen:

- **[Próximo]**: migración a Kotlin y Firebase, recordatorios desde el servidor, panel web y publicación en Google Play.
- **[1.0.0] — 2026-10-18 (actual)**: app funcional en MIT App Inventor, .apk, .aia, código QR, informe final y presentación.
- **[0.3.0] — 2026-10-18**: arquitectura por capas, modelo entidad-relación en 3FN, prevención de doble reserva, entidad y DAO de Room, Room como fuente única de verdad.
- **[0.2.0] — Módulos 3 y 4**: proyecto base en Compose, Firebase Authentication y navegación por rol.
- **[0.1.0] — 2026-08-31**: borrador inicial, repositorio y wiki.

## Cómo ejecutar el proyecto

**Prototipo (MIT App Inventor):** instala `app/CitaFacil.apk` en un teléfono Android, o importa `app/CitaFacil.aia` en [ai2.appinventor.mit.edu](https://ai2.appinventor.mit.edu) con *Projects › Import project (.aia)* y pruébalo con MIT AI2 Companion.

**Versión comercial (en desarrollo):**

1. Instala la versión más reciente de Android Studio.
2. Clona el repositorio:
   ```bash
   git clone https://github.com/Carlos200322/desarrollo-de-aplicacion-android.git
   ```
3. Crea un proyecto en Firebase, registra la app Android y coloca `google-services.json` en `app/`.
4. Abre el proyecto, sincroniza Gradle y ejecuta en un emulador o dispositivo con Android 8.0 o superior.

## Reflexión

El proyecto recorrió el ciclo completo de una app móvil: del problema de negocio a un archivo instalable. Cada foro aportó una pieza: la arquitectura por capas de Google, que se conservó con procedimientos en el editor de bloques; el diseño de bases de datos, que se tradujo en la regla contra la doble reserva; el uso responsable del SMS a través de la app de mensajería del usuario; la localización, que inspiró el botón «Cómo llegar»; y el trabajo en segundo plano y la distribución en Google Play, que definieron la hoja de ruta. La principal limitación es que las citas se guardan solo en el teléfono; compartir la agenda en tiempo real con Cloud Firestore es la siguiente meta.

## Referencias

Codd, E. F. (1970). A relational model of data for large shared data banks. *Communications of the ACM, 13*(6), 377–387. https://doi.org/10.1145/362384.362685

Elmasri, R., & Navathe, S. B. (2016). *Fundamentals of database systems* (7.ª ed.). Pearson.

Google. (2026a). *Android 17*. Android Developers. https://developer.android.com/about/versions/17

Google. (2026b). *Cloud Firestore*. Firebase Documentation. https://firebase.google.com/docs/firestore

Google. (2026c). *Guide to app architecture*. Android Developers. https://developer.android.com/topic/architecture

Google. (2026d). *Save data in a local database using Room*. Android Developers. https://developer.android.com/training/data-storage/room

Google. (2026e). *Transactions and batched writes*. Firebase Documentation. https://firebase.google.com/docs/firestore/manage-data/transactions

Lacan, O. (s.f.). *Keep a Changelog* (versión 1.1.0). https://keepachangelog.com/es-ES/1.1.0/

Massachusetts Institute of Technology. (s.f.). *MIT App Inventor*. https://appinventor.mit.edu

McLean, S. M., Booth, A., Gee, M., Salway, S., Cobb, M., Bhanbhro, S., & Nancarrow, S. A. (2016). Appointment reminder systems are effective but not optimal: Results of a systematic review and evidence synthesis employing realist principles. *Patient Preference and Adherence, 10*, 479–499. https://doi.org/10.2147/PPA.S93046

Preston-Werner, T. (s.f.). *Versionado semántico 2.0.0*. https://semver.org/lang/es/

Wolber, D., Abelson, H., Spertus, E., & Looney, L. (2014). *App Inventor 2: Create your own Android apps* (2.ª ed.). O’Reilly Media.
