# C# Study Guide - Collections, LINQ, Files & Razor

## Generic Classes

```csharp
// 1. Single-type generic container
public class Contenidor<T>
{
    private T valor;
    
    public Contenidor(T valor) { this.valor = valor; }
    
    public void Mostrar() { Console.WriteLine(valor); }
}

// Usage
var c1 = new Contenidor<int>(42);
var c2 = new Contenidor<string>("Hola");
var c3 = new Contenidor<DateTime>(DateTime.Now);

// 2. Two-type generic container
public class Parella<T1, T2>
{
    private T1 primer;
    private T2 segon;
    
    public Parella(T1 primer, T2 segon) 
    { 
        this.primer = primer; 
        this.segon = segon;
    }
    
    public void Mostrar() 
    { 
        Console.WriteLine($"Primer: {primer}, Segon: {segon}"); 
    }
}

// Usage
var p1 = new Parella<string, int>("Clau", 42);
var p2 = new Parella<double, bool>(3.14, true);
```

## Non-Generic Collections

```csharp
// ArrayList example
ArrayList llista = new ArrayList();
llista.Add(42);
llista.Add("Hola");
llista.Add(3.14);
llista.Add(true);

// Iterate
foreach (var item in llista)
{
    Console.WriteLine(item);
}

// Remove
llista.Remove(3.14);

// Hashtable example
Hashtable taula = new Hashtable();
taula.Add("Nom", "Joan");
taula.Add("Edat", 25);

// Display values
foreach (DictionaryEntry /*var should work as well*/ entry in taula)
{
    Console.WriteLine($"{entry.Key}: {entry.Value}");
}

// Check if key exists
bool existeix = taula.ContainsKey("Ciutat");
```

## Generic Collections

```csharp
// List<T> example
List<int> numeros = new List<int> { 10, 20, 30, 40, 50 };
numeros.Add(60);           // Add at end
numeros.Remove(30);        // Remove value
numeros.Sort();            // Sort ascending
numeros.Reverse();         // Reverse order (for descending)

// Dictionary<K,V> example
Dictionary<string, double> preus = new Dictionary<string, double>
{
    {"Poma", 1.20},
    {"Llet", 0.90},
    {"Pa", 1.50}
};

// Display prices
foreach (var item in preus)
{
    Console.WriteLine($"{item.Key}: {item.Value}€");
}

// Check if product exists
bool existeix = preus.ContainsKey("Aigua");
```

## Collection Sorting

```csharp
// Sorting strings
List<string> noms = new List<string> { "Anna", "Joan", "Maria", "Pere" };
noms.Sort();                 // Alphabetical
noms.Reverse();              // Reverse alphabetical

// Sorting numbers
List<int> nums = new List<int> { 15, 3, 8, 20, 10 };
nums.Sort();                 // Ascending
// LINQ for descending sort
var descendents = nums.OrderByDescending(n => n).ToList();
```

## LINQ Where Clause

```csharp
// Filter numbers
List<int> nums = new List<int> { 5, 10, 15, 20, 25, 30 };

// Even numbers
var parells = nums.Where(n => n % 2 == 0).ToList();

// Numbers > 15
var grans = nums.Where(n => n > 15).ToList();

// Filter strings
List<string> paraules = new List<string> { "Hola", "Món", "C#", "Programació", "LINQ" };

// Words starting with "P"
var ambP = paraules.Where(p => p.StartsWith("P")).ToList();
```

## DateTime Operations

```csharp
// Days until a specific date
Console.Write("Enter a date (dd/MM/yyyy): ");
string dataStr = Console.ReadLine();
DateTime data = DateTime.ParseExact(dataStr, "dd/MM/yyyy", null);
int diesFaltants = (data - DateTime.Today).Days;
Console.WriteLine($"Dies que falten: {diesFaltants}");

// Get current day of week
string diaSetmana = DateTime.Today.DayOfWeek.ToString();
Console.WriteLine($"Avui és {diaSetmana}");

// Calculate age
Console.Write("Enter birth date (dd/MM/yyyy): ");
string naixementStr = Console.ReadLine();
DateTime naixement = DateTime.ParseExact(naixementStr, "dd/MM/yyyy", null);
int edat = DateTime.Today.Year - naixement.Year;
if (DateTime.Today < naixement.AddYears(edat)) edat--;
Console.WriteLine($"Edat: {edat} anys");
```

## Advanced LINQ

```csharp
// LINQ operations
List<int> nums = new List<int> { 12, 5, 8, 20, 15, 10 };

// Numbers > 10 with their squares
var gransIQuadrats = nums.Where(n => n > 10)
                          .Select(n => new { Numero = n, Quadrat = n * n });
foreach (var item in gransIQuadrats)
{
    Console.WriteLine($"{item.Numero} → {item.Quadrat}");
}

// Count occurrences
int comptador = nums.Count(n => n == 5);

// String operations
List<string> llenguatges = new List<string> { "C#", "Java", "Python", "JavaScript", "C++" };

// Strings with more than 3 characters
var llargs = llenguatges.Where(l => l.Length > 3).ToList();

// Character frequency
var freqCaracters = llenguatges.SelectMany(l => l)
                               .GroupBy(c => c)
                               .Select(g => new { Caracter = g.Key, Frequencia = g.Count() });
```

## LINQ with Multiple Collections

```csharp
// Classes
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Order
{
    public int OrderId { get; set; }
    public int CustomerId { get; set; }
    public string Product { get; set; }
}

// Data
List<Customer> customers = new List<Customer>
{
    new Customer { Id = 1, Name = "Anna" },
    new Customer { Id = 2, Name = "Marc" }
};

List<Order> orders = new List<Order>
{
    new Order { OrderId = 101, CustomerId = 1, Product = "Ordinador" },
    new Order { OrderId = 102, CustomerId = 1, Product = "Telèfon" },
    new Order { OrderId = 103, CustomerId = 2, Product = "Tauleta" }
};

// Join collections
var customerOrders = from c in customers
                     join o in orders on c.Id equals o.CustomerId
                     select new { CustomerName = c.Name, Product = o.Product };

foreach (var item in customerOrders)
{
    Console.WriteLine($"{item.CustomerName} ha comprat {item.Product}");
}
```

## Plain text file operations

```csharp
// Reading Text File
public void LlegirTextFile()
{
    using (var reader = new StreamReader("log.txt"))
    {
        string line;
        while ((line = reader.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }
}

// Writing to Text File (Append Mode)
public void EscriureTextFileAppend()
{
    // The true parameter enables append mode
    using (var writer = new StreamWriter("log.txt", true))
    {
        writer.WriteLine($"{DateTime.Now}: Application started");
        writer.WriteLine($"{DateTime.Now}: Operation completed");
    } // File is automatically closed when the using block ends
}

// Writing to Text File (Overwrite Mode)
public void EscriureTextFile()
{
    // Without the true parameter, the file is overwritten
    using (var writer = new StreamWriter("log.txt"))
    {
        writer.WriteLine("Nova entrada de log");
        writer.WriteLine("Segona entrada de log");
    }
}
```

## CSV Operations (CsvHelper)

```csharp
// Reading CSV
public void LlegirCSV()
{
    using (var reader = new StreamReader("empleats.csv"))
    using (var csv = new CsvReader(reader, CultureInfo.InvariantCulture))
    {
        var records = csv.GetRecords<Empleat>().ToList();
        
        foreach (var emp in records)
        {
            Console.WriteLine($"{emp.Nom} {emp.Cognom}");
        }
    }
}

// Writing CSV
public void EscriureCSV()
{
    var empleats = new List<Empleat>
    {
        new Empleat { Id = 1, Nom = "Joan", Cognom = "Garcia", Edat = 30 },
        new Empleat { Id = 2, Nom = "Anna", Cognom = "Martínez", Edat = 25 }
    };
    
    using (var writer = new StreamWriter("empleats.csv"))
    using (var csv = new CsvWriter(writer, CultureInfo.InvariantCulture))
    {
        csv.WriteRecords(empleats);
    }
}
```

## JSON Operations (System.Text.Json)

```csharp
// Reading JSON
public void LlegirJSON()
{
    using (var reader = new StreamReader("empleats.json"))
    {
        string json = reader.ReadToEnd();
        var empleats = JsonSerializer.Deserialize<List<Empleat>>(json);
        
        foreach (var emp in empleats)
        {
            Console.WriteLine($"{emp.Nom} {emp.Cognom}");
        }
        
        // Filtering
        var empsGrans = empleats.Where(e => e.Edat > 26).ToList();
    }
}

// Writing JSON
public void EscriureJSON()
{
    var empleats = new List<Empleat>
    {
        new Empleat { Id = 1, Nom = "Joan", Cognom = "Garcia", Edat = 30 },
        new Empleat { Id = 2, Nom = "Anna", Cognom = "Martínez", Edat = 25 }
    };
    
    var options = new JsonSerializerOptions { WriteIndented = true };
    string json = JsonSerializer.Serialize(empleats, options);
    
    using (var writer = new StreamWriter("empleats.json"))
    {
        writer.Write(json);
    }
}
```

## XML Operations (System.Xml.Linq)

```csharp
// Reading XML
public void LlegirXML()
{
    XDocument doc = XDocument.Load("empleats.xml");
    
    // Query with LINQ
    var empleats = from emp in doc.Descendants("empleat")
                   select new
                   {
                       Nom = emp.Element("nom").Value,
                       Cognom = emp.Element("cognom").Value
                   };
    
    foreach (var emp in empleats)
    {
        Console.WriteLine($"{emp.Nom} {emp.Cognom}");
    }
    
    // Filtering with LINQ
    var empsGrans = from emp in doc.Descendants("empleat")
                    where int.Parse(emp.Element("edat").Value) > 26
                    select new
                    {
                        Nom = emp.Element("nom").Value,
                        Edat = int.Parse(emp.Element("edat").Value)
                    };
}

// Writing XML
public void CrearXML()
{
    var empleats = new List<Empleat>
    {
        new Empleat { Id = 1, Nom = "Joan", Cognom = "Garcia", Edat = 30 },
        new Empleat { Id = 2, Nom = "Anna", Cognom = "Martínez", Edat = 25 }
    };
    
    XDocument doc = new XDocument(
        new XElement("empleats",
            from emp in empleats
            select new XElement("empleat",
                new XElement("id", emp.Id),
                new XElement("nom", emp.Nom),
                new XElement("cognom", emp.Cognom),
                new XElement("edat", emp.Edat)
            )
        )
    );
    
    // Auto-close with using
    using (var writer = new StreamWriter("empleats.xml"))
    {
        doc.Save(writer);
    }
}
```

## Razor Pages

```csharp
// Model with validation
public class Empleat
{
    [Required(ErrorMessage = "El nom és obligatori")]
    public string Nom { get; set; }
    
    [Required(ErrorMessage = "El cognom és obligatori")]
    public string Cognom { get; set; }
    
    [Range(18, 100, ErrorMessage = "L'edat ha de ser entre 18 i 100")]
    public int Edat { get; set; }
    
    [Required(ErrorMessage = "El departament és obligatori")]
    public string Departament { get; set; }
}

// Custom validation
public class EdatMajorDe18Attribute : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, ValidationContext validationContext)
    {
        int edat = (int)value;
        if (edat <= 18)
        {
            return new ValidationResult("L'edat ha de ser major de 18 anys");
        }
        
        return ValidationResult.Success;
    }
}
```

**Razor Page (.cshtml)**
```html
@page
@model CreateModel
@{
    ViewData["Title"] = "Nou Empleat";
}

<h1>Nou Empleat</h1>

<form method="post">
    <div class="form-group">
        <label asp-for="Empleat.Nom"></label>
        <input asp-for="Empleat.Nom" class="form-control" />
        <span asp-validation-for="Empleat.Nom" class="text-danger"></span>
    </div>
    
    <div class="form-group">
        <label asp-for="Empleat.Cognom"></label>
        <input asp-for="Empleat.Cognom" class="form-control" />
        <span asp-validation-for="Empleat.Cognom" class="text-danger"></span>
    </div>
    
    <div class="form-group">
        <label asp-for="Empleat.Edat"></label>
        <input asp-for="Empleat.Edat" class="form-control" />
        <span asp-validation-for="Empleat.Edat" class="text-danger"></span>
    </div>
    
    <div class="form-group">
        <label asp-for="Empleat.Departament"></label>
        <input asp-for="Empleat.Departament" class="form-control" />
        <span asp-validation-for="Empleat.Departament" class="text-danger"></span>
    </div>
    
    <button type="submit" class="btn btn-primary">Guardar</button>
</form>

<!-- For displaying a list of employees -->
<table class="table">
    <thead>
        <tr>
            <th>Nom</th>
            <th>Cognom</th>
            <th>Edat</th>
            <th>Departament</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var emp in Model.Empleats)
        {
            <tr>
                <td>@emp.Nom</td>
                <td>@emp.Cognom</td>
                <td>@emp.Edat</td>
                <td>@emp.Departament</td>
            </tr>
        }
    </tbody>
</table>
```

**Razor Page Model (.cs)**
```csharp
public class CreateModel : PageModel
{
    [BindProperty]
    public Empleat Empleat { get; set; }
    
    public List<Empleat> Empleats { get; set; }
    
    public void OnGet()
    {
        // Load the list of employees
        Empleats = GetEmpleats(); // Method to get employees
    }
    
    public IActionResult OnPost()
    {
        if (!ModelState.IsValid)
        {
            return Page();
        }
        
        // Save the employee
        SaveEmpleat(Empleat);
        
        return RedirectToPage("./Index");
    }
}
```
