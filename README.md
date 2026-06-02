<div align="center">

<img src="https://img.shields.io/badge/C%2B%2B%2FCLI-.NET%20Framework-blue?style=for-the-badge&logo=cplusplus&logoColor=white"/>

<img src="https://img.shields.io/badge/ESP32--S3-Arduino-red?style=for-the-badge&logo=arduino&logoColor=white"/>

<img src="https://img.shields.io/badge/BLE-Bluetooth%20Low%20Energy-blueviolet?style=for-the-badge&logo=bluetooth&logoColor=white"/>

<img src="https://img.shields.io/badge/LVGL-v9.4-orange?style=for-the-badge"/>

<img src="https://img.shields.io/badge/Estado-Sprint%203%20Completado-success?style=for-the-badge"/>

# 🩺 Sistema Integrado de Asistencia y Gestión de Salud

**Programación Orientada a Objetos — PUCP 2026**

*Sistema de monitoreo para adultos mayores con integración BLE al smartwatch JNOX*

</div>

---

## 📋 Descripción del Proyecto

Este proyecto consiste en el desarrollo de un **sistema de escritorio** que actúa como central de monitoreo y gestión para adultos mayores, integrándose vía **Bluetooth Low Energy (BLE)** de forma nativa asíncrona con el smartwatch **JNOX**, dispositivo basado en el microcontrolador **ESP32-S3**.

El sistema permite a familiares, cuidadores y personal técnico:
- 👤 Administrar perfiles clínicos de pacientes de forma personalizada.
- ⏰ Programar recordatorios diarios (medicación, citas, rutinas) sincronizados con el reloj.
- 🚨 Recibir alertas críticas en tiempo real ante caídas detectadas por el acelerómetro **BMI160**.
- 🩻 Gestionar recomendaciones médicas por parte de profesionales de salud.
- 🛠️ Proveer soporte técnico, diagnóstico de comunicación y administración global de usuarios mediante un perfil de **Mantenimiento**.

---

## 🏗️ Arquitectura del Sistema

### Arquitectura en Capas Consolidada (Sprint 3)

El software de escritorio se compone de tres proyectos organizados bajo un patrón en capas, donde la persistencia de datos local en archivos planos se encuentra integrada directamente en la capa de controladores para evitar dependencias dinámicas (DLL) redundantes y optimizar los accesos a disco:

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                        CAPA DE PRESENTACIÓN (GUI)                            │
│   Windows Forms (.exe): Login, Panel de Pacientes, Detalle de Paciente,      │
│   Dispositivos (Reloj), Recordatorios, Historial Clínico, Dashboard Médico,  │
│   Dashboard Técnico, Mantenimiento de Usuarios, Modal de Emparejamiento BLE │
├──────────────────────────────────────────────────────────────────────────────┤
│               CAPA DE CONTROLADOR Y PERSISTENCIA (Controller)                │
│   Maneja las reglas de negocio y almacena los datos locales en archivos TXT. │
│   UsuarioController · PacienteController · ContactoController                │
│   EventoController · DispositivoController · MantenimientoController         │
│   Archivos: usuarios.txt · pacientes.txt · contactos.txt · eventos.txt       │
│             dispositivos.txt                                                 │
├──────────────────────────────────────────────────────────────────────────────┤
│                              CAPA DE MODELO (Model)                          │
│   Entidades del sistema y canal de comunicación asíncrono con hardware.      │
│   Usuario · Cuidador · Medico · Mantenimiento                                │
│   Paciente · ContactoEmergencia · DispositivoJNOX                            │
│   EventoSalud · Recordatorio · AlertaCaida · GestorBLE (WinRT BLE Nativo)    │
└──────────────────────────────────────────────────────────────────────────────┘
```

### Integración con Hardware (BLE Nativo)

La aplicación de escritorio se conecta de forma **nativa asíncrona** (sin scripts intermediarios) al smartwatch JNOX utilizando la API `Windows.Devices.Bluetooth` de Windows Runtime (WinRT), permitiendo un flujo de telemetría constante:

```
┌─────────────────────────────────┐        BLE (JSON)         ┌─────────────────────────────┐
│   Aplicación de Escritorio      │ ◄───────────────────────► │     Smartwatch JNOX         │
│        (C++/CLI - WinRT)        │                           │     (ESP32-S3 + LVGL)       │
│                                 │                           │                             │
│  • Gestión de Pacientes y Roles │                           │  • Interfaz LVGL v9.4       │
│  • Programación de Recordatorios│                           │  • Acelerómetro BMI160      │
│  • Panel del Cuidador/Médico/Téc│                           │  • Buzzer de Alerta/Vibrac. │
│  • Handshake de Seguridad JSON  │                           │  • Confirmación de Caída    │
│  • Historial Clínico y Eventos  │                           │  • Handshake y RTC Sync     │
└─────────────────────────────────┘                           └─────────────────────────────┘
```

---

## 📐 Diagrama de Clases — Modelo Completo

El sistema completo está compuesto por clases estructuradas y organizadas de forma lógica en las capas de la solución:

### Capa Model — Jerarquía de Usuarios
```
Usuario (base)
├── Cuidador
├── Médico
└── Mantenimiento (técnico)
```

### Capa Model — Jerarquía de Eventos de Salud
```
EventoSalud (base)
├── Recordatorio
└── AlertaCaída
```

### Capa Model — Clases Independientes
```
Paciente · ContactoEmergencia · DispositivoJNOX · GestorBLE
```

### Capa Controller y Persistencia
```
UsuarioController · PacienteController · ContactoController
EventoController · DispositivoController · MantenimientoController
```

### Resumen de Clases del Modelo

| Clase | Tipo | Atributos principales | Métodos destacados |
|---|---|---|---|
| `Usuario` | Clase base | `Id`, `Nombre`, `Edad`, `Telefono`, `UsuarioLogin`, `Contrasena` | constructor |
| `Cuidador` | Hereda de Usuario | `Turno`, `PacientesAsignadosIDs (List)` | constructor, `AsignarPaciente()`, `DesasignarPaciente()` |
| `Médico` | Hereda de Usuario | `Especialidad`, `CentroDeSalud`, `PacientesAsignadosIDs (List)` | constructor, `AsignarPaciente()`, `DesasignarPaciente()` |
| `Mantenimiento` | Hereda de Usuario | — | `AdministrarAgregarUsuario()`, `AdministrarEditarUsuario()`, `AdministrarEliminarUsuario()`, `AdministrarListarUsuarios()`, `SolucionarProblemasReloj()` |
| `Paciente` | Independiente | `IDPaciente`, `NombrePaciente`, `TipoSangre`, `SeguroMedico`, `RecomendacionesMedicas (List)`, `ContactosEmergenciaIDs (List)`, `HistorialMedico` | `addRecomendacion()`, `addContactoEmergencia()`, `addHistorial()`, `showPacienteInfo()` |
| `ContactoEmergencia` | Independiente | `IDCE`, `IDPacienteAsociado`, `NombreCompleto`, `Parentesco`, `Telefono` | `MostrarContactoEmergencia()` |
| `DispositivoJNOX` | Independiente | `DireccionMAC`, `NivelBateria`, `EstadoConexion`, `UmbralLibre`, `UmbralImpacto`, `Sensibilidad`, `VentanaMs` | `setConfigCaida()`, `getNivelBat()` |
| `EventoSalud` | Clase base eventos | `IdEvento`, `IDPacienteAsociado`, `FechaHora`, `Estado` | `setEstado()`, `getEstado()` |
| `Recordatorio` | Hereda de EventoSalud | `MensajeTexto`, `Categoria`, `Repetir`, `IntervaloMinutos` | `setIntervalo()` |
| `AlertaCaída` | Hereda de EventoSalud | `MagnitudImpacto`, `ConfirmacionReloj` | `caidaDetectada()`, `getMagnitudCaida()` |
| `GestorBLE` | Independiente | `EstaConectado`, `MacConectada`, `UltimaBateria`, `HandshakeRealizado` | `ConectarReloj()`, `ScanBLEDevices()`, `EnviarRecordatorio()`, `EnviarConfigCaida()`, `Desconectar()` |

### Resumen de Clases Controller (Con Persistencia Integrada)

| Controlador | Archivo de datos | Métodos CRUD / Proxy | Característica especial / Reglas de negocio |
|---|---|---|---|
| `UsuarioController` | `usuarios.txt` | SaveUsuario, QueryAll, QueryById, Update, Delete | Autenticación (`ValidarLogin()`), asignación polimórfica de pacientes a Médicos/Cuidadores. |
| `PacienteController` | `pacientes.txt` | SavePaciente, QueryAll, QueryById, Update, Delete | Recomendaciones médicas serializadas con separador `\|`, vinculación directa con smartwatch mediante MAC. |
| `ContactoController` | `contactos.txt` | SaveContacto, QueryAll, QueryById, Delete | Filtrado y consulta por ID de Paciente asociado. |
| `EventoController` | `eventos.txt` | SaveEvento, QueryAll, Update, Delete | Persistencia polimórfica de Recordatorios y Alertas de Caída. |
| `DispositivoController` | `dispositivos.txt` | SaveDispositivo, QueryAll, QueryByMAC, Update | Gestión de parámetros de caída (umbrales y sensibilidad del sensor BMI160). Clave por MAC. |
| `MantenimientoController`| `usuarios.txt` | Agregar, Editar, Eliminar y Listar Usuarios | Lógica de soporte técnico (`SolucionarProblemasReloj()`) y mantenimiento global del sistema. |

---

## ✅ Historias de Usuario

| ID | Actor | Historia |
|---|---|---|
| HU01 | Cuidador | Registrarme en el sistema para guardar mis datos de acceso y personales |
| HU02 | Cuidador | Administrar los datos clínicos del adulto mayor (tipo de sangre, seguro, contactos) |
| HU03 | Cuidador | Agendar recordatorios por categoría para que el reloj JNOX alerte al paciente |
| HU04 | Adulto mayor | Recibir alertas de vibración y texto legible en el reloj al momento de una actividad |
| HU05 | Cuidador | Ser notificado visual y auditivamente cuando el paciente registre una caída |
| HU06 | Adulto mayor | Confirmar desde el reloj que estoy a salvo para cancelar la alarma de caída |
| HU07 | Cuidador | Visualizar el historial de alarmas y caídas detectadas por paciente |
| HU08 | Médico | Acceder a la información del paciente y registrar recomendaciones médicas |
| HU09 | Técnico | Iniciar sesión con un perfil técnico para acceder a herramientas de mantenimiento y diagnóstico |
| HU10 | Técnico | Gestionar (crear, editar, eliminar) a todos los usuarios del sistema (médicos, cuidadores y técnicos) |
| HU11 | Técnico | Diagnosticar y resolver problemas de conexión y estado del smartwatch JNOX directamente desde la aplicación |

---

## 🗂️ Product Backlog

### Sprint 1 — ✅ Completado

- [x] Modelado y programación de las 10 clases en C++/CLI (encapsulamiento, herencia, polimorfismo)
- [x] Diseño del Diagrama de Clases en StarUML con jerarquías y relaciones completas
- [x] Configuración del entorno de desarrollo para JNOX HEALTH en Arduino con LVGL v9.4
- [x] Configuración del repositorio en GitHub y tablero de seguimiento en Trello
- [x] Redacción del Catálogo de Requisitos e Historias de Usuario

### Sprint 2 — ✅ Completado

- [x] Reestructuración del proyecto bajo el patrón de arquitectura en capas (Model, Persistence, Controller, GUI)
- [x] Modificación de clases del modelo para soportar persistencia (nuevos atributos de vinculación entre entidades)
- [x] Implementación de 5 clases de acceso a datos (Persistence) con operaciones CRUD y persistencia en archivos `.txt`
- [x] Implementación de 5 clases de control (Controller) como intermediarios entre GUI y Persistence
- [x] Desarrollo de interfaces gráficas: `frmLogin`, `JnoxMainForm` (MDI), `frmMantenimientoUsuario`, `frmMantenimientoPaciente`, `frmMantenimientoDispositivo`
- [x] Desarrollo de prototipos transaccionales: `frmMonitoreoAlertas` y `frmHistorialClinico`
- [x] Actualización del Diagrama de Clases en StarUML con las 20 clases del sistema

### Sprint 3 — ✅ Completado

- [x] **Comunicación BLE Nativa Real**: Migración completa de scripts externos (`scan_ble.ps1`) a una implementación nativa en C++/CLI con Windows Runtime (`Windows::Devices::Bluetooth::Advertisement`).
- [x] **Emparejamiento y Vinculación en Caliente**: Formulario modal de escaneo (`BluetoothGridForm`) que asocia y persiste la dirección MAC del dispositivo al perfil del paciente de forma dinámica.
- [x] **Handshake Seguro de 2 Vías (JSON)**: Validación asíncrona mediante palabra clave en el ESP32-S3 y sincronización horaria de RTC con el reloj del sistema operativo.
- [x] **Módulo de Soporte y Diagnóstico Técnico**: Incorporación del rol de `Mantenimiento` con interfaz gráfica dedicada para solución de fallas físicas y reseteos (`MantenimientoDipositivos.h`).
- [x] **Panel de Administración de Usuarios**: Pantalla CRUD independiente (`Usuarios.h`) para la gestión completa de credenciales y roles del sistema.
- [x] **Reestructuración y Consolidación de Capas**: Fusión de la capa de persistencia con la capa de controladores para simplificar la estructura de desarrollo y remover redundancias de DLLs.
- [x] **Deconflicto de Hardware y Seguridad en ESP32-S3**: Temporizador anti-intrusión de 5s en firmware y deinit/init limpio del stack BLE entre modos HID y HEALTH.

### Pendiente (Sprints futuros)

- [ ] Optimización para escaneo BLE asíncrono continuo sin detención temporal del watcher.
- [ ] Mapeo e implementación de persistencia para IDs de paciente únicos por usuario si se requiere a futuro.
- [ ] Refactorización para mover el grid de pacientes programático (`dgvPacientes`) directamente al `InitializeComponent` del diseñador de Visual Studio.
- [ ] Limpieza periódica automática de registros de MACs inválidas en `dispositivos.txt`.

---

## 🛠️ Tecnologías

| Componente | Tecnología |
|---|---|
| Aplicación de escritorio | C++/CLI (.NET Framework 4.7.2) — Visual Studio 2022 |
| Interfaz de usuario | Windows Forms (MDI & Diseñador Visual Studio) |
| Persistencia de datos | Archivos de texto plano (.txt) gestionados por la capa Controller |
| Comunicación inalámbrica | Bluetooth Low Energy (BLE) Nativo vía Windows Runtime (WinRT / UWP) |
| Protocolo de datos | Tramas JSON seguras bidireccionales |
| Firmware del reloj | Arduino / C++ (ESP32-S3 - NimBLE Stack) |
| Interfaz gráfica del reloj | LVGL v9.4 |
| Sensor de caídas | Acelerómetro de 6 ejes BMI160 (Protocolo I2C) |
| Control de versiones | Git / GitHub |
| Gestión de proyecto | Trello |

---

## 👥 Equipo

| Integrante | Código | Responsabilidad Sprint 1 | Responsabilidad Sprint 2 | Responsabilidad Sprint 3 |
|---|---|---|---|---|
| Javier Armando Bonilla Flores | 20234901 | Configuración entorno JNOX HEALTH (Arduino + LVGL v9.4) | Arquitectura en capas, migración Model, modificaciones al modelo, frmMonitoreoAlertas | Implementación nativa de WinRT BLE en GestorBLE.cpp/h, deconflicto de hardware ESP32 y handshake seguro. |
| Esthefany Hualparuca Sedano | 20236286 | Diseño del Diagrama de Clases en StarUML | Actualización del Diagrama de Clases (20 clases), frmMantenimientoPaciente, ContactoAccesoDatos/Controller | Diseño e integración de MantenimientoDipositivos.h y lógica de troubleshooting en Mantenimiento.h. |
| Anthony Aquiles William Tufiño Ugarte | 20226010 | Programación de las 10 clases en C++/CLI | UsuarioAccesoDatos, PacienteAccesoDatos, UsuarioController, PacienteController, frmLogin, frmMantenimientoUsuario, frmMantenimientoDispositivo | Integración del rol Mantenimiento (Técnico), control de usuarios en Usuarios.h y refactorización de controladores. |
| Piero Elguera Quichcas | 20236454 | GitHub, Trello y redacción del Catálogo de Requisitos | EventoAccesoDatos, DispositivoAccesoDatos, EventoController, DispositivoController, frmHistorialClinico, JnoxMainForm, Trello Sprint 2 | DashboardMantenimientoForm.h, sincronización RTC, pruebas asíncronas de tramas JSON y Trello Sprint 3. |

---

## 📁 Estructura del Repositorio

```
JNOX_MODO_HEALTH/ (Repositorio GitHub)
├── MejoraCodigoPOO/                   ← Solución principal de la App de Escritorio
│   ├── CodigoPOO.sln                 ← Archivo de Solución de Visual Studio
│   ├── CONTEXTO_JNOX.txt              ← Documento maestro de contexto técnico
│   ├── CLAUDE.md                      ← Guía de reglas críticas de desarrollo
│   │
│   ├── JNOX_Model/                    ← Proyecto de Capa de Modelo (DLL)
│   │   ├── pch.h / pch.cpp
│   │   ├── Usuario.h / .cpp           (Clase base de usuarios)
│   │   ├── Cuidador.h / .cpp
│   │   ├── Medico.h / .cpp
│   │   ├── Mantenimiento.h / .cpp     (Usuario Técnico - Mantenimiento)
│   │   ├── Paciente.h / .cpp
│   │   ├── ContactoEmergencia.h / .cpp
│   │   ├── DispositivoJNOX.h / .cpp
│   │   ├── EventoSalud.h / .cpp
│   │   ├── Recordatorio.h / .cpp
│   │   ├── AlertaCaida.h / .cpp
│   │   └── GestorBLE.h / .cpp         (Módulo BLE nativo con WinRT)
│   │
│   ├── JNOX_Controller/               ← Proyecto de Capa de Control y Persistencia (DLL)
│   │   ├── pch.h / pch.cpp
│   │   ├── UsuarioController.h / .cpp
│   │   ├── PacienteController.h / .cpp
│   │   ├── ContactoController.h / .cpp
│   │   ├── EventoController.h / .cpp
│   │   ├── DispositivoController.h / .cpp
│   │   └── MantenimientoController.h / .cpp (Control técnico y soluciones)
│   │
│   └── JNOX_GUI/                      ← Proyecto de Capa de Presentación (EXE)
│       ├── MyForm.h / MyForm.cpp       (MDI Main Form de navegación)
│       ├── LOGIN.h / LOGIN.cpp         (Pantalla de login del sistema)
│       ├── PACIENTES.h / PACIENTES.cpp (CRUD/Gestor de Pacientes)
│       ├── PacienteForm.h / .cpp       (Diálogo para agregar/editar pacientes y contactos)
│       ├── DISPOSITIVOS.h / .cpp       (Controlador de relojes y configuración de caídas)
│       ├── Recordatorio.h / .cpp       (Programador de recordatorios y alertas)
│       ├── Dashboard.h / .cpp           (Panel de monitoreo de caídas del Cuidador/Médico)
│       ├── HistorialDeEventos.h / .cpp (Historial clínico general)
│       ├── BluetoothGridForm.h         (Diálogo de escaneo y emparejamiento BLE)
│       ├── CONFIGURACION.h / .cpp
│       ├── AsignarPacienteForm.h / .cpp
│       ├── SesionActual.h / .cpp       (Gestor estático de sesión global)
│       ├── Navegacion.h / .cpp
│       │
│       ├── DashboardMantenimientoForm.h / .cpp (Dashboard exclusivo del Técnico)
│       ├── Usuarios.h / .cpp           (CRUD general de usuarios para el Técnico)
│       ├── MantenimientoDipositivos.h / .cpp (Panel de soporte técnico y diagnóstico)
│       │
│       └── [Datos persistidos (.txt)]  (Generación y lectura automática en base dir)
│           ├── usuarios.txt
│           ├── pacientes.txt
│           ├── contactos.txt
│           ├── eventos.txt
│           └── dispositivos.txt
│
├── FIRMWARE_JNOX/                     ← Código fuente del Smartwatch (ESP32-S3)
│   ├── JNOX_MODO_HEALTH.ino            ← Sketch principal del reloj
│   ├── health_mode.ino                 ← Máquina de estados y ticks de salud
│   ├── health_ble.ino                  ← GATT Server BLE y publicidad NimBLE
│   ├── health_ui.ino                   ← Pantallas LVGL v9.4
│   ├── health_bmi160.ino               ← Driver I2C del sensor y algoritmos de caída
│   └── jnox_common.h                   ← Cabeceras globales del reloj
│
├── ENTREGABLES/                       ← Informes académicos del curso
│   ├── ENTREGABLE 1/
│   ├── ENTREGABLE 2/
│   └── ENTREGABLE 3/                  ← Carpeta para el entregable final
└── README.md                          ← Este archivo de documentación (también README.txt)
```

---

## 🚀 Cómo ejecutar y Compilar

### Requisitos del Sistema Operativo para BLE Nativo
Para permitir que la aplicación clásica Win32 acceda de forma nativa a las APIs de Windows Runtime (UWP) para Bluetooth, es indispensable realizar lo siguiente en el PC de ejecución:
1. Activar el **Modo Desarrollador** de Windows (Configuración -> Privacidad y Seguridad -> Para desarrolladores -> Modo de desarrollador: Activado).
2. Emparejar o limpiar cachés antiguos del dispositivo si Windows ya lo reconoció previamente en otro modo (HID/Bluetooth clásico).

### Aplicación de Escritorio (C++/CLI)

1. Clonar el repositorio:
   ```bash
   git clone https://github.com/C-137J/ProyectoPOO_2026_PUCP.git
   ```
2. Abrir `CodigoPOO.sln` ubicado en `MejoraCodigoPOO/` con **Visual Studio 2022** (o superior).
3. Seleccionar la configuración de compilación `Debug | x64` (indispensable para la arquitectura de bibliotecas de Windows).
4. **IMPORTANTE para la compilación**: Para evitar errores de enlazado como `LNK2022` (tipos CLR duplicados por caché del enlazador), se debe compilar haciendo un **Rebuild completo** del proyecto:
   - Click derecho en la Solución -> **Recompilar solución** (Rebuild Solution).
   - O usando MSBuild en PowerShell:
     ```powershell
     Get-Process JNOX_GUI -ErrorAction SilentlyContinue | Stop-Process -Force
     & MSBuild.exe CodigoPOO.sln /t:Rebuild /p:Configuration=Debug /p:Platform=x64 /nologo /v:minimal
     ```
5. Presionar **F5** o click en **Iniciar** para ejecutar.

> **Nota:** Los archivos de datos (`.txt`) se leen y actualizan de forma automática en la carpeta de ejecución. Si se cierra la app, todos los cambios se guardan persistentemente en sus respectivos ficheros planos.

### Firmware JNOX (ESP32-S3)

1. Instalar [Arduino IDE](https://www.arduino.cc/en/software) con soporte para placas ESP32-S3 (versión de placa `arduino-esp32` recomendada: 3.3.7).
2. Añadir la librería **LVGL v9.4** desde el gestor de librerías.
3. Abrir el sketch correspondiente en `FIRMWARE_JNOX/`.
4. Seleccionar la placa `ESP32S3 Dev Module` y el puerto COM asignado.
5. Cargar el firmware al smartwatch.

---

## 📄 Documentación
- 📋 Tablero Trello: *https://trello.com/b/Uoays2LN/proyecto-poo*

---

<div align="center">

**Pontificia Universidad Católica del Perú — Facultad de Ciencias e Ingeniería**

*Programación Orientada a Objetos · 2026*

</div>
