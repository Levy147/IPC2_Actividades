# Reporte: Arquitectura Multi-Nivel y Patrón MVC

## Parte 1: Fundamentación Teórica

### 1.1 Sistemas Distribuidos y Arquitectura Multi-Nivel

**Limitación del monolito local:** Cuando la interfaz, la lógica y el almacenamiento están en una sola máquina, el sistema no escala bien. Si aumentan los usuarios, todo compite por los mismos recursos (CPU, RAM, disco) y la base de datos puede bloquearse. Tampoco es fácil sincronizar datos entre varias sedes o servicios.

**Capas (Layers) vs. Niveles (Tiers):**
- **Layers:** Organización lógica del código (carpetas Model, View, Controller).
- **Tiers:** Distribución física en servidores separados (presentación, aplicación, datos).

**Los 3 niveles físicos:**
1. **Presentación:** Muestra la interfaz al usuario (HTML, CSS, navegador).
2. **Aplicación/Negocio:** Procesa reglas, validaciones y seguridad (ASP.NET Core, APIs).
3. **Datos:** Guarda y recupera información (SQL Server, PostgreSQL, listas en memoria).

**Seguridad perimetral:** Exponer el puerto de una base de datos a internet es un error crítico porque atacantes podrían acceder directamente a los datos. La buena práctica es colocar la BD en una red privada y permitir conexiones solo desde el servidor de aplicación (Nivel 2).

### 1.2 Patrón MVC

**Código espagueti:** Mezclar SQL, lógica de negocio y HTML en un solo archivo dificulta el mantenimiento y hace casi imposible escribir pruebas unitarias.

**Separación de responsabilidades (SoC):**
- **Modelo:** Representa los datos y reglas del dominio. No sabe cómo se muestran.
- **Vista:** Pantalla pasiva que solo renderiza datos. No debe contener SQL ni lógica de negocio.
- **Controlador:** Recibe la petición HTTP, coordina al Modelo y elige qué Vista mostrar.

**Alta cohesión y bajo acoplamiento:** Cada componente tiene una responsabilidad clara (cohesión) y puede modificarse con poco impacto en los demás (bajo acoplamiento).

---

## Parte 2: Enrutamiento

### 2.1 Mapeo de URLs

Plantilla: `{controller=Home}/{action=Index}/{id?}`

| URL del cliente | Controlador | Acción | Parámetro id |
| :--- | :--- | :--- | :--- |
| `.../ControlAcademico/Login` | `ControlAcademicoController` | `Login` | (Ninguno) |
| `.../Estudiante/Historial/20260123` | `EstudianteController` | `Historial` | `20260123` |
| `.../Asignacion/Detalle/10` | `AsignacionController` | `Detalle` | `10` |
| `.../Home` | `HomeController` | `Index` (por defecto) | (Ninguno) |

### 2.2 Flujo de una petición HTTP (1 al 5)

1. El usuario hace clic en un botón; el navegador envía una petición HTTP al servidor.
2. El enrutador de ASP.NET Core lee la URL y selecciona el **Controlador** y la **Acción** correspondientes.
3. El **Controlador** recibe la petición y obtiene o prepara los datos (del **Modelo** o de una fuente simulada).
4. El **Modelo** devuelve los datos al Controlador sin preocuparse por la presentación.
5. El Controlador pasa los datos a la **Vista**, que genera el HTML y lo envía al navegador del usuario.

---

## Parte 3: Implementación Práctica

Proyecto ASP.NET Core MVC con:
- `Models/Estudiante.cs` — entidad POCO.
- `Controllers/EstudianteController.cs` — controlador delgado con `Listar` (GET) y `Registrar` (POST).
- `Views/Estudiante/Listar.cshtml` — vista de listado.
- `Program.cs` — enrutamiento convencional habilitado.

**Pruebas:**
- GET en navegador: `https://localhost:PORT/Estudiante/Listar`
- POST con Postman: `POST /Estudiante/Registrar` con body JSON:
  ```json
  { "carne": 2026099, "nombre": "Juan Pérez", "promedio": 88.5 }
  ```

---

## Parte 5: Referencias Bibliográficas

> Facultad de Ingeniería, USAC. (2026). Sesión 11: Modelado Base y Arquitecturas de Despliegue. Evolución de Sistemas Distribuidos, Fundamentos del Modelo Cliente-Servidor y Diseño Físico Multi-Capas (N-Tier). Laboratorio del curso Introducción a la Programación y Computación 2. Guatemala.

> Facultad de Ingeniería, USAC. (2026). Sesión 12: Arquitectura y Componentes del Patrón MVC. Desacoplamiento Lógico de Software, Ciclo de Vida de las Peticiones y Enrutamiento en Aplicaciones Interactivas Modernas. Laboratorio del curso Introducción a la Programación y Computación 2. Guatemala.
