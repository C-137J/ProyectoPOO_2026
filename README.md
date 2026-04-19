# ProyectoPOO_2026
# Sistema Integrado de Asistencia y Gestión de Salud (JNOX HEALTH)

[cite_start]Un sistema de escritorio desarrollado en C++/CLI que actúa como una central de monitoreo y gestión para adultos mayores, integrado vía Bluetooth Low Energy (BLE) con el smartwatch JNOX (basado en ESP32-S3)[cite: 610, 615]. [cite_start]Este proyecto busca mejorar la seguridad del usuario y los tiempos de respuesta del personal de salud mediante la gestión de perfiles, recordatorios diarios y alertas críticas por caídas en tiempo real[cite: 611].

---

## 🚀 Características Principales

El sistema resuelve las siguientes Historias de Usuario clave:
* [cite_start]**Gestión Clínica (CRUD):** Registro de cuidadores y administración estructurada de datos de pacientes (tipo de sangre, contactos de emergencia)[cite: 619, 620].
* [cite_start]**Recordatorios Bidireccionales:** Programación de tomas de medicamentos y eventos categorizados desde la PC, los cuales activan alertas de vibración y texto en el smartwatch[cite: 621, 622].
* [cite_start]**Detección de Caídas Críticas:** El reloj utiliza su acelerómetro integrado (BMI160) para detectar impactos y solicitar confirmación al usuario para calmar el aviso[cite: 617, 624].
* [cite_start]**Centro de Emergencias en PC:** Recepción de tramas de emergencia vía BLE que desencadenan notificaciones visuales y auditivas en el entorno de Windows del cuidador[cite: 623].
* [cite_start]**Monitoreo e Historial:** Visualización gráfica en la aplicación de escritorio de todas las ocurrencias y alarmas detectadas por cada paciente[cite: 625].

---

## 🛠️ Arquitectura y Tecnologías

[cite_start]El ecosistema está dividido en dos grandes bloques que se comunican concurrentemente mediante tramas JSON a través de BLE[cite: 633, 651]:

### [cite_start]1. Aplicación de Escritorio (Centro de Comando) [cite: 613]
* [cite_start]**Lenguaje/Framework:** C++/CLI (.NET Framework)[cite: 638].
* [cite_start]**Interfaz Gráfica:** Windows Forms (Formularios para Login, Dashboard y CRUD)[cite: 630].
* [cite_start]**Conectividad:** `Windows.Devices.Bluetooth` gestionado mediante hilos concurrentes para evitar el bloqueo de la aplicación principal[cite: 632, 633].
* [cite_start]**Persistencia:** Sistema de guardado para objetos de pacientes e historiales[cite: 631].

### [cite_start]2. Smartwatch JNOX (Hardware Wearable) [cite: 615]
* [cite_start]**Microcontrolador:** ESP32-S3[cite: 615].
* [cite_start]**Sensores:** Acelerómetro BMI160 comunicado vía protocolo I2C[cite: 617, 635].
* [cite_start]**Interfaz Gráfica (Wearable):** LVGL v9.4 programado en entorno Arduino[cite: 605, 634].

---

## 📦 Modelo de Dominio (Capa de Clases)

[cite_start]El proyecto implementa el paradigma Orientado a Objetos con 11 clases base[cite: 628]:

1. [cite_start]**`Usuario`**: Clase abstracta base (`Id`, `Nombre`, `Edad`, `Telefono`, `UsuserLogin`, `Contraseña`)[cite: 639].
2. [cite_start]**`Cuidador`** (Hereda de `Usuario`): Gestiona su `Turno` y una `Lista de PacientesAsignados`[cite: 640].
3. [cite_start]**`Medico`** (Hereda de `Usuario`): Administra `Especialidad`, `CentrodeSalud`, `Lista de PacientesAsignados` y un método para generar recomendaciones médicas[cite: 643].
4. [cite_start]**`Paciente`**: Contiene `TipoSangre`, `seguroMedico`, vinculaciones directas a su `DispositivoJNOX`, `Lista de Contactos Emergencia`, `Historial Medico` y `Recomendaciones Medicas`[cite: 641, 642].
5. [cite_start]**`ContactoEmergencia`**: Almacena `UsuarioLogin`, `Contrasena`, `Nombre Completo`, `Parentesco` y `Telefono`[cite: 644].
6. [cite_start]**`DispositivoJNOX`**: Representa el hardware físico (`DireccionMAC`, `NivelBateria`, `EstadoConexion`) y aloja un objeto de configuración de caída[cite: 645].
7. [cite_start]**`ConfiguracionCaida`**: Define los parámetros de calibración del sensor (`UmbralLibre`, `Umbrallmpacto`, `Sensibilidad`, `Tiempo VentanaMs`)[cite: 646].
8. [cite_start]**`EventoSalud`**: Estructura de sucesos globales (`IdEvento`, `FechaHora`, `Estado`)[cite: 647].
9. [cite_start]**`Recordatorio`** (Hereda de `EventoSalud`): Contiene la lógica y el texto que se desplegará en la pantalla del reloj[cite: 648].
10. [cite_start]**`AlertaCaida`** (Hereda de `EventoSalud`): Registra la `Magnituddellmpacto` procesada por el sensor inercial[cite: 649].
11. [cite_start]**`GestorBLE`**: Clase controladora con rutinas `ConectarReloj(mac)`, `Desconectar()`, y serialización/deserialización JSON para el puente entre C++/CLI y el receptor físico[cite: 650, 651].

---

## 👥 Equipo de Desarrollo

* [cite_start]**Javier:** Programación e integración de JNOX HEALTH (Arduino, I2C para BMI160, Vistas en LVGL v9.4)[cite: 605].
* [cite_start]**Anthony:** Creación de la solución en Visual Studio e implementación del código C++/CLI aplicando encapsulamiento, herencia y polimorfismo[cite: 607].
* [cite_start]**Esthefany:** Arquitectura del software, modelado y diseño del Diagrama de Clases (StarUML)[cite: 606].
* [cite_start]**Piero:** Gestión ágil, administración de repositorio (GitHub), seguimiento de Sprints (Trello) y redacción técnica de requisitos e Historias de Usuario[cite: 608].
