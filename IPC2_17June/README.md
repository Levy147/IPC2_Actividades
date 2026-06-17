# Reporte: Arquitectura Multi-Nivel y Patrón MVC

Este repositorio explica de forma sencilla cómo se estructuran los programas modernos, separando dónde se instalan (Niveles físicos) y cómo se organiza su código (Patrón MVC).

---

## Parte 1: Sistemas Distribuidos y Arquitectura Multi-Nivel

### 1. El Problema del "Monolito"
Un sistema monolítico es aquel donde la pantalla, la lógica y la base de datos están instalados en una misma computadora. 
* **El problema:** Si el sistema necesita crecer, la única opción es comprar una computadora más cara. Además, si muchos usuarios entran al mismo tiempo, el sistema se satura y la base de datos se bloquea.

### 2. Diferencia entre Capas (Layers) y Niveles (Tiers)
* **Capas (Layers):** Es la organización lógica. Básicamente, cómo ordenamos las carpetas y los archivos de código dentro de nuestro proyecto.
* **Niveles (Tiers):** Es la separación física. Representa en qué servidor o computadora real está instalada cada parte del sistema.

### 3. Los 3 Niveles Físicos (3-Tier)
1. **Nivel 1 (Presentación):** Es la cara del sistema. Se encarga únicamente de mostrar la información al usuario y capturar sus clics o textos. 
   * *Ejemplos:* HTML, CSS, React, Blazor.
2. **Nivel 2 (Aplicación):** Es el cerebro. Aquí se hacen los cálculos, se aplican las reglas del negocio y se valida la seguridad.
   * *Ejemplos:* Servidores en C# (.NET), Node.js o Java.
3. **Nivel 3 (Datos):** Es la memoria. Su único trabajo es guardar y recuperar información de forma segura.
   * *Ejemplos:* SQL Server, PostgreSQL, MongoDB.

### 4. Seguridad de la Base de Datos
Nunca se debe exponer una base de datos directamente a internet, ya que los hackers podrían atacarla fácilmente. La regla de oro es que la base de datos debe estar en una red privada y **solo** el Nivel de Aplicación (Nivel 2) tiene permiso para comunicarse con ella.

---

## Parte 2: El Patrón de Código MVC

### 1. El Problema del "Código Espagueti"
Ocurre cuando mezclamos consultas de base de datos (SQL), cálculos matemáticos y diseño visual (HTML) en un solo archivo. Esto es una mala práctica porque hace que el código sea casi imposible de leer, modificar o probar.

### 2. La Solución: Modelo, Vista y Controlador (MVC)
El patrón MVC divide el código en tres partes para mantener todo ordenado:

* **Modelo:** Maneja los datos y las reglas. No le importa cómo se ve la aplicación, solo le importan los datos.
* **Vista:** Es la pantalla. Su única tarea es mostrar lo que el usuario ve. Tiene prohibido hacer cálculos o conectarse a la base de datos.
* **Controlador:** Es el intermediario. Recibe las peticiones del usuario, le pide la información al Modelo y se la pasa a la Vista para que la muestre.

### 3. Ventajas (Métricas de Ingeniería)
Usar MVC nos da un código con **Alta Cohesión** (cada archivo hace una sola cosa bien) y **Bajo Acoplamiento** (podemos cambiar por completo el diseño de la Vista sin tener que tocar el código del Modelo).

---

## Parte 3: Ciclo de Vida y Enrutamiento en .NET

### 1. Mapeo de URLs
En ASP.NET Core, las rutas siguen la estructura: `{Controlador}/{Acción}/{ID}`.

| URL que escribe el Cliente | Controlador que responde | Método que se ejecuta | Parámetro recibido |
| :--- | :--- | :--- | :--- |
| `.../ControlAcademico/Login` | `ControlAcademicoController` | `Login` | (Ninguno) |
| `.../Estudiante/Historial/20260123` | `EstudianteController` | `Historial` | `20260123` |
| `.../Asignacion/Detalle/10` | `AsignacionController` | `Detalle` | `10` |
| `.../Home` | `HomeController` | `Index` (por defecto) | `(Ninguno)` |

### 2. El Flujo Paso a Paso
Cuando un usuario hace clic en un botón, este es el viaje de la información:

1. **Petición:** El navegador envía la solicitud HTTP al servidor.
2. **Ruta:** El sistema lee la URL y decide qué Controlador debe atenderla.
3. **Procesamiento:** El Controlador recibe la petición y le pide al Modelo que busque o calcule los datos necesarios.
4. **Respuesta interna:** El Modelo termina su trabajo y le devuelve los datos listos al Controlador.
5. **Pantalla final:** El Controlador le entrega esos datos a la Vista, la cual genera el HTML final y se lo manda de regreso al usuario para que lo vea en su pantalla.