# CitaFácil — Aplicación de gestión de citas y recordatorios

**Borrador del proyecto | Módulo 1**
**Autor:** Carlos
**Curso:** Desarrollo de Aplicaciones Android
**Repositorio:** https://github.com/Carlos200322/desarrollo-de-aplicacion-android

---

## 1. Descripción del proyecto

CitaFácil es una aplicación móvil nativa para Android orientada a negocios de servicios que operan mediante citas programadas, específicamente barberías, salones de belleza y clínicas de atención ambulatoria de pequeño y mediano tamaño. La aplicación permite que el cliente consulte la disponibilidad real de cada profesional, reserve su turno sin llamar por teléfono y reciba recordatorios automáticos antes de la cita. Del otro lado, el administrador del negocio gestiona la agenda, define horarios y servicios, y consulta indicadores básicos de ocupación y ausentismo.

El proyecto se desarrollará a lo largo del término académico en incrementos sucesivos: primero la autenticación y el modelo de datos, después el flujo de reserva, luego el panel administrativo y finalmente las notificaciones y los reportes. El objetivo pedagógico es recorrer el ciclo completo de desarrollo Android, desde la definición del problema empresarial hasta la generación de un paquete distribuible, aplicando control de versiones y pruebas en cada etapa.

## 2. Exposición del problema

Los negocios de servicios por cita enfrentan tres problemas operativos concretos que erosionan directamente sus ingresos.

**Pérdida de tiempo administrativo.** La reserva por llamada telefónica o mensajería instantánea obliga al personal a interrumpir su trabajo para atender el teléfono. En una barbería con un solo empleado, cada llamada interrumpe un servicio en curso y degrada la experiencia del cliente presente. La agenda en papel, además, no permite consultar disponibilidad de forma inmediata ni evitar la doble reserva del mismo horario.

**Ausentismo sin aviso.** El cliente que no se presenta ni cancela deja un espacio vacío que ya no puede venderse. La literatura sobre servicios de salud documenta este fenómeno de manera consistente y muestra que los recordatorios automáticos reducen de forma significativa la tasa de inasistencia (McLean et al., 2016). Para un negocio pequeño, cada hora vacía representa una pérdida directa e irrecuperable.

**Ausencia de datos para decidir.** Sin registro digital, el propietario no sabe cuáles son sus horas de mayor demanda, qué servicio genera más ingreso ni qué clientes han dejado de volver. Las decisiones sobre horarios, contratación de personal o promociones se toman por intuición.

CitaFácil aborda estos tres problemas con una sola solución: digitalizar la agenda, automatizar los recordatorios y registrar cada transacción para producir información útil.

## 3. Plataforma

El proyecto se desarrollará como aplicación **nativa para Android** utilizando **Kotlin** y **Android Studio**, con arquitectura **MVVM** y **Jetpack Compose** para la construcción de interfaces declarativas.

La elección del desarrollo nativo sobre alternativas multiplataforma responde a razones técnicas y contextuales. En primer lugar, la aplicación requiere notificaciones locales y push confiables, integración con el calendario del dispositivo y funcionamiento parcial sin conexión, capacidades donde el acceso directo a las APIs del sistema resulta más estable. En segundo lugar, Android concentra la mayor base instalada en los mercados donde operan estos negocios, lo que hace innecesaria una versión para iOS en la primera fase. En tercer lugar, el objetivo académico del curso es dominar el ecosistema Android, y una capa de abstracción multiplataforma ocultaría precisamente los componentes que se busca comprender.

La aplicación tendrá como objetivo el nivel de **API 37 (Android 17)**, versión actual publicada en el Proyecto de Código Abierto de Android, con un `minSdk` en API 26 (Android 8.0) para cubrir dispositivos de gama de entrada aún vigentes (Google, 2026a). Apuntar al nivel de API más reciente no es opcional en el ecosistema comercial: Google Play exige periódicamente que las aplicaciones publicadas apunten a versiones recientes para permanecer disponibles en la tienda.

### Componentes técnicos

| Capa | Tecnología | Función |
|---|---|---|
| Interfaz | Jetpack Compose, Material 3 | Construcción declarativa de pantallas |
| Arquitectura | MVVM, ViewModel, StateFlow | Separación de lógica y presentación |
| Persistencia local | Room | Caché de citas para uso sin conexión |
| Backend en la nube | Cloud Firestore | Sincronización de la agenda en tiempo real |
| Autenticación | Firebase Authentication | Registro por teléfono y correo |
| Notificaciones | Firebase Cloud Messaging, WorkManager | Recordatorios programados |
| Construcción | Gradle | Gestión de dependencias y compilación |
| Control de versiones | Git y GitHub | Historial, ramas e integración continua |

La decisión de usar una arquitectura híbrida —persistencia local con Room más sincronización en la nube con Firestore— se justifica por el patrón de uso real: el cliente puede consultar su próxima cita sin cobertura, mientras el administrador necesita ver los cambios de agenda de forma inmediata en cualquier dispositivo. Firebase resulta apropiado en esta escala porque elimina la administración de servidores y permite que un desarrollador individual entregue funcionalidad completa; para un volumen mayor o requisitos regulatorios de datos de salud, la arquitectura debería migrar a un backend propio (Google, 2026b).

## 4. Interfaz de usuario e interfaz de administrador

La aplicación contempla dos perfiles con permisos y vistas diferenciadas, resueltos mediante control de acceso basado en roles almacenados en el perfil del usuario autenticado.

### Interfaz del cliente

El cliente accede a un flujo deliberadamente corto, diseñado para completar una reserva en menos de un minuto y con el mínimo de decisiones por pantalla:

- Registro e inicio de sesión mediante número telefónico o correo electrónico.
- Selección del servicio deseado, con duración y precio visibles antes de continuar.
- Elección del profesional, o bien la opción de aceptar el primero disponible.
- Calendario con los horarios libres, donde los ocupados aparecen deshabilitados en lugar de ocultos, para que el usuario perciba la disponibilidad real.
- Confirmación con resumen de la cita y opción de agregarla al calendario del dispositivo.
- Pantalla de "Mis citas" con historial, y botones para cancelar o reprogramar dentro de la ventana permitida.
- Recepción de recordatorios automáticos veinticuatro horas y dos horas antes.

### Interfaz del administrador

El administrador dispone de una vista operativa y otra de configuración:

- Agenda del día en formato de línea de tiempo, con estados diferenciados por color: confirmada, en curso, completada, cancelada y ausencia.
- Registro manual de citas para clientes que acuden sin reserva o llaman por teléfono.
- Gestión del catálogo de servicios, con nombre, duración, precio y profesionales habilitados.
- Configuración de horarios de atención, días festivos y bloqueos temporales por vacaciones o ausencia.
- Administración del personal y de sus horarios individuales.
- Panel de indicadores con ocupación por franja horaria, servicios más solicitados, ingresos estimados del período y tasa de ausentismo.
- Envío de notificaciones puntuales, por ejemplo ante un cierre imprevisto.

## 5. Funcionalidad

Los requisitos se organizan en funcionales y no funcionales.

### Requisitos funcionales

1. **RF-01.** El sistema debe permitir el registro y la autenticación de usuarios diferenciando el rol de cliente y el de administrador.
2. **RF-02.** El sistema debe mostrar únicamente los horarios disponibles según la duración del servicio y la agenda del profesional seleccionado.
3. **RF-03.** El sistema debe impedir la doble reserva de un mismo horario mediante una transacción atómica en la base de datos.
4. **RF-04.** El sistema debe enviar recordatorios automáticos programados antes de la cita.
5. **RF-05.** El sistema debe permitir la cancelación y reprogramación dentro de una ventana configurable por el administrador.
6. **RF-06.** El sistema debe conservar el historial de citas de cada cliente.
7. **RF-07.** El sistema debe permitir al administrador crear, modificar y eliminar servicios, horarios y personal.
8. **RF-08.** El sistema debe generar indicadores de ocupación, ingresos y ausentismo por período.
9. **RF-09.** El sistema debe permitir la consulta de las citas próximas sin conexión a internet.

### Requisitos no funcionales

- **Rendimiento.** Las pantallas principales deben responder en menos de dos segundos con una conexión móvil estándar.
- **Usabilidad.** El flujo de reserva no debe superar cuatro pasos, y la aplicación debe cumplir los lineamientos de accesibilidad de Material Design en contraste y tamaño de objetivos táctiles.
- **Seguridad.** Los datos personales se transmitirán cifrados y el acceso a la información se controlará mediante reglas de seguridad declaradas en Firestore, de modo que ningún cliente pueda leer las citas de otro.
- **Disponibilidad.** La consulta de la agenda propia debe funcionar en modo sin conexión a partir de la caché local.
- **Mantenibilidad.** El código seguirá una arquitectura por capas con separación entre datos, dominio y presentación, y contará con pruebas unitarias sobre la lógica de disponibilidad.

## 6. Diseño (wireframes)

Los esquemas siguientes describen la estructura de las pantallas principales. Son representaciones de baja fidelidad centradas en la disposición de elementos y en la jerarquía de la información, no en el estilo visual final.

### Pantalla 1 — Inicio del cliente

```
+--------------------------------+
|  CitaFácil            [perfil] |
+--------------------------------+
|  Hola, Carlos                  |
|                                |
|  PRÓXIMA CITA                  |
|  +--------------------------+  |
|  | Corte de cabello         |  |
|  | Vie 12 sep · 10:30 a.m.  |  |
|  | Con: Luis                |  |
|  | [Reprogramar] [Cancelar] |  |
|  +--------------------------+  |
|                                |
|  +--------------------------+  |
|  |    RESERVAR NUEVA CITA   |  |
|  +--------------------------+  |
|                                |
|  Historial                   > |
+--------------------------------+
| [Inicio] [Citas] [Ajustes]     |
+--------------------------------+
```

### Pantalla 2 — Selección de servicio y horario

```
+--------------------------------+
| <  Reservar cita               |
+--------------------------------+
|  1. Servicio                   |
|  ( ) Corte        30 min  $10  |
|  (•) Corte + barba 45 min $15  |
|  ( ) Afeitado     20 min   $8  |
|                                |
|  2. Profesional                |
|  [ Luis v ]                    |
|                                |
|  3. Fecha                      |
|  L  M  M  J  V  S  D           |
|  8  9 10 11 12 13 14           |
|        [12]                    |
|                                |
|  4. Hora                       |
|  [09:00] [09:45] [~10:30~]     |
|  [11:15] [12:00] [~12:45~]     |
|                                |
|  +--------------------------+  |
|  |        CONTINUAR         |  |
|  +--------------------------+  |
+--------------------------------+
```

*Los horarios entre virgulillas aparecen deshabilitados por estar ocupados.*

### Pantalla 3 — Agenda del administrador

```
+--------------------------------+
| Agenda        < 12 sep 2026 >  |
+--------------------------------+
| Ocupación: 78%   Ausencias: 1  |
+--------------------------------+
| 09:00 | Ana R.   | Corte    ●  |
| 09:45 | LIBRE            [ + ] |
| 10:30 | Carlos M.| Corte+B  ●  |
| 11:15 | Jorge P. | Afeitado ○  |
| 12:00 | LIBRE            [ + ] |
| 12:45 | Sara L.  | Corte    ✕  |
+--------------------------------+
| ● Confirmada ○ En curso ✕ Falta|
+--------------------------------+
| [Agenda] [Servicios] [Reportes]|
+--------------------------------+
```

## 7. Trabajo previsto para el término académico

| Módulo | Entregable |
|---|---|
| 1–2 | Borrador del proyecto, repositorio y wiki |
| 3–4 | Autenticación, modelo de datos y navegación |
| 5–6 | Flujo completo de reserva y persistencia local |
| 7 | Panel administrativo y reportes |
| 8 | Notificaciones, pruebas y generación del paquete final |

## 8. Referencias

Google. (2026a). *Android 17*. Android Developers. https://developer.android.com/about/versions/17

Google. (2026b). *Cloud Firestore*. Firebase Documentation. https://firebase.google.com/docs/firestore

Google. (2026c). *Guide to app architecture*. Android Developers. https://developer.android.com/topic/architecture

McLean, S. M., Booth, A., Gee, M., Salway, S., Cobb, M., Bhanbhro, S., & Nancarrow, S. A. (2016). Appointment reminder systems are effective but not optimal: Results of a systematic review and evidence synthesis employing realist principles. *Patient Preference and Adherence*, 10, 479–499. https://doi.org/10.2147/PPA.S93046
