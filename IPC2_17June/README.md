# Reporte Analítico: Arquitectura Multi-Nivel (N-Tier) y Patrón MVC

Este repositorio contiene la fundamentación teórica y el análisis crítico sobre la evolución de los sistemas distribuidos, la separación de responsabilidades a nivel físico y lógico, y la implementación del patrón arquitectónico Modelo-Vista-Controlador (MVC).

---

## 1. El Tránsito hacia los Sistemas Distribuidos y Multi-Capa

### La Limitación del Monolito Local
Cuando la interfaz, la lógica de negocio y el almacenamiento residen de forma exclusiva en una única máquina aislada, la escalabilidad se vuelve estrictamente vertical, dependiendo de la adquisición de hardware más potente. Además, la sincronización de datos es inviable para múltiples usuarios concurrentes porque el estado no se comparte en red, lo que genera cuellos de botella operativos y bloqueos transaccionales a nivel de base de datos.

### Distinción Crítica: Layers vs. Tiers
*   **Capas Lógicas (Layers):** Se refieren a la organización interna del código fuente (namespaces, carpetas, ensamblados) para mantener una separación estructurada de las responsabilidades lógicas dentro del software.
*   **Niveles Físicos (Tiers):** Representan la infraestructura de despliegue real; es decir, las máquinas físicas, servidores o contenedores distintos donde se ejecuta de manera aislada cada capa lógica.

### Responsabilidades en la Arquitectura de 3 Niveles
1.  **Nivel 1 (Presentation Tier):** Tiene la misión exclusiva de interactuar con el usuario final, capturar sus entradas y mostrar la información procesada.
    *   *Tecnología común:* Navegadores web, HTML/CSS/JS, React, Angular, Blazor.
2.  **Nivel 2 (Application Tier):** Se encarga de procesar las reglas del negocio, los cálculos lógicos y las validaciones de seguridad centralizadas.
    *   *Tecnología común:* Servidores backend con C# (.NET Core), Node.js, Java.
3.  **Nivel 3 (Data Tier):** Su responsabilidad es garantizar la persistencia, integridad y recuperación segura de la información a largo plazo.
    *   *Tecnología común:* Motores de bases de datos relacionales o NoSQL (SQL Server, PostgreSQL, MongoDB).

### Seguridad Perimetral
Exponer el puerto de una base de datos directamente a internet representa un error crítico de ingeniería que amplía la superficie de ataque, facilitando accesos no autorizados, ataques de fuerza bruta o inyecciones de código. La práctica estándar exige alojar la base de datos dentro de una red privada virtual (VPC) y configurar estrictas reglas de firewall para que acepte conexiones de forma exclusiva desde la dirección IP autorizada del Application Tier (Nivel 2).

---

## 2. Desacoplamiento Lógico con el Patrón MVC

### La Crisis del Código Espagueti
Mezclar sentencias de acceso a datos (SQL), lógica matemática y etiquetas visuales (HTML) dentro de un mismo archivo físico destruye la mantenibilidad del software. Esta práctica imposibilita la reutilización del código, dificulta el rastreo de errores (debugging) y hace inviable la implementación de pruebas unitarias (Unit Testing), ya que no se puede probar una regla de negocio sin invocar dependencias de la interfaz gráfica o de la base de datos.

### Separación de Preocupaciones (SoC)
El patrón MVC resuelve este problema aislando los componentes en tres entidades con responsabilidades exclusivas:

*   **Modelo (Model):** Representa las estructuras de datos, el estado de la aplicación y las reglas de negocio subyacentes. Es una entidad agnóstica a la interfaz; no debe conocer ni importarle cómo se exponen los datos al cliente.
*   **Vista (View):** Es una entidad pasiva encargada exclusivamente de la presentación visual. Tiene estrictamente prohibido contener código de lógica de negocio, cálculos matemáticos complejos o consultas directas a los repositorios de datos.
*   **Controlador (Controller):** Actúa como intermediario táctico y director de flujo. Intercepta las peticiones HTTP entrantes, solicita la información procesada al Modelo y transfiere esos datos limpios a la Vista correspondiente para su renderizado.

### Métricas de Ingeniería de Software
La implementación estricta del patrón MVC garantiza una **Alta Cohesión**, obligando a que cada componente tenga una única razón para cambiar (Single Responsibility Principle). De forma paralela, asegura un **Bajo Acoplamiento**, dado que los componentes interactúan mediante contratos e interfaces abstractas, permitiendo, por ejemplo, rediseñar completamente la interfaz gráfica de usuario sin necesidad de alterar una sola línea de código en la lógica de negocio o en el enrutamiento.