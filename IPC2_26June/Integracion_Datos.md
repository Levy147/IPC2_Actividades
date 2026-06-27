# Actividad de Laboratorio: Interoperabilidad y Carga Masiva de Datos

**Modalidad:** Individual
**Archivo:** Integracion_Datos.md

---

## Parte 1: Evaluación Conceptual y Buenas Prácticas

**Formatos de Intercambio:**

| Formato | Ventajas | Desventajas |
| :--- | :--- | :--- |
| **CSV** | Formato muy ligero y rápido de procesar; ideal para transferir grandes volúmenes de datos puramente tabulares; fácil de leer tanto por humanos como por sistemas sencillos. | No soporta estructuras de datos anidadas o jerárquicas; carece de metadatos integrados o un esquema estricto de validación estándar. |
| **XML** | Soporta estructuras jerárquicas y relacionales complejas; es autodescriptivo; permite validación estricta de estructura mediante esquemas (XSD). | Es muy verboso, lo que genera archivos más pesados; requiere mayor consumo de CPU y memoria (RAM) para su parseo en comparación a otros formatos. |

**1. Diferenciación de Procesos:**
Utilizando la librería nativa `System.Text.Json`:
*   **Serialización:** Es el proceso de convertir el estado de un objeto que vive en la memoria de nuestra aplicación en C# hacia un formato de texto estándar (como una cadena JSON), permitiendo que los datos sean transmitidos por una red o almacenados en un archivo.
*   **Deserialización:** Es el proceso inverso, donde se toma una cadena de texto en formato JSON (proveniente de un API, por ejemplo) y se reconstruye para instanciar un objeto tipado en C# en memoria, haciéndolo utilizable por la lógica del programa.

**2. El Antipatrón del Rendimiento:**
El antipatrón **"N+1"** en el contexto de carga masiva ocurre cuando el sistema realiza una llamada o inserción a la base de datos por *cada* registro individual leído del archivo (1 consulta inicial + N inserciones individuales). Esto genera un cuello de botella crítico por el exceso de viajes a través de la red y bloqueos en la base de datos.
*   **Estrategia de Optimización (Batching):** Para solucionarlo, se deben leer y mapear los registros agrupándolos en una colección intermedia en memoria (como una `List<T>`). Una vez procesado el archivo, se inserta todo el lote de registros de un solo golpe en la base de datos utilizando comandos transaccionales masivos (como `AddRange()` y `SaveChangesAsync()`), reduciendo el impacto en el servidor a una sola transacción.

---

## Parte 2: Implementación Práctica en C#

### Desafío 1: Consumo de Endpoints y Deserialización

using System.Net.Http;
using System.Text.Json;
using System.Threading.Tasks;

public async Task<Alumno> ObtenerAlumnoAsync()
{
    try
    {
        // Petición GET
        var response = await _httpClient.GetAsync("https://api.usac.edu/v1/alumnos");
        response.EnsureSuccessStatusCode(); // Validación de estado

        // Extraer texto y configurar opciones en menos líneas
        var json = await response.Content.ReadAsStringAsync();
        var opciones = new JsonSerializerOptions { PropertyNameCaseInsensitive = true };

        // Retornar directamente la deserialización
        return JsonSerializer.Deserialize<Alumno>(json, opciones);
    }
    catch
    {
        // Manejo de excepciones simplificado
        throw;
    }
}


Desafío 2: Endpoint para Carga Masiva CSV


using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

[HttpPost("carga-masiva")]
public async Task<IActionResult> CargarArchivoCsv(IFormFile archivoCsv)
{
    var listaRegistros = new List<RegistroAcademico>();

    // Declaración 'using' simplificada (C# 8.0+) para ahorrar llaves {}
    using var stream = archivoCsv.OpenReadStream();
    using var reader = new StreamReader(stream);
    
    string lineaActual;
    
    // Ciclo de lectura asíncrona
    while ((lineaActual = await reader.ReadLineAsync()) != null)
    {
        var columnas = lineaActual.Split(',');
        
        // Creación e inserción a la lista en un solo paso
        listaRegistros.Add(new RegistroAcademico { 
            Carnet = columnas[0], 
            Nombre = columnas[1] 
        });
    }

    // Batching: Inserción y guardado
    _context.Registros.AddRange(listaRegistros);
    await _context.SaveChangesAsync();

    return Ok("Carga exitosa.");
}