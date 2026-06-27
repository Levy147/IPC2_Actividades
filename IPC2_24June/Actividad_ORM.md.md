# Actividad Corta de Laboratorio: De ADO.NET Tradicional a la Automatización con EF Core

## Parte 1: Diagnóstico Técnico y Brecha de Impedancia

**1. La Brecha de Impedancia**
* **Clase Clásica (POCO)** -> Mapea a -> **Tabla**
* **Propiedad/Atributo** -> Mapea a -> **Columna**
* **Instancia de Objeto** -> Mapea a -> **Fila (o Registro)**

**2. Mitigación de Vulnerabilidades**
EF Core utiliza parametrización automática en sus consultas LINQ, lo que previene la Inyección SQL al separar las sentencias de los datos. En ADO.NET, el comando equivalente que usábamos para mitigarlo era agregar parámetros de forma manual utilizando `cmd.Parameters.AddWithValue()`.

**3. Optimización de Infraestructura**
El método `.AsNoTracking()` apaga el rastreador de cambios (Change Tracker) de EF Core. Al usarlo en consultas que son exclusivamente de lectura, el ORM no guarda una copia del estado de los objetos en la memoria RAM, lo que libera memoria crítica y optimiza el consumo de recursos en el servidor.

---

## Parte 2: Desafío de Refactorización de Código

**1. El Contexto (DbContext)**

```csharp
using Microsoft.EntityFrameworkCore;

public class UnidadAcademicaContext : DbContext{
    public UnidadAcademicaContext(DbContextOptions<UnidadAcademicaContext> options) : base(options){
    }

    public DbSet<Catedratico> Catedraticos { get; set; }
}

2- Consulta
public List<Catedratico> ObtenerCatedraticosViejos(UnidadAcademicaContext _context){
    return _context.Catedraticos
        .AsNoTracking()
        .Where(c => c.Nombre.StartsWith("Ing."))
        .ToList();
}

Facultad de Ingeniería, USAC. (2026). Sesión 17: Conectividad con SQL Server. Acceso Estructurado a Datos mediante C# y ADO.NET. Laboratorio de Introducción a la Programación y Computación 2. Guatemala.

Facultad de Ingeniería, USAC. (2026). Sesión 18: Mapeo de Objetos Relacionales. Persistencia Automatizada con Entity Framework Core. Laboratorio de Introducción a la Programación y Computación 2. Guatemala.