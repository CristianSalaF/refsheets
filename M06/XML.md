## Understanding the Three XML Approaches

**[XMLReader/XMLWriter](./XML-RW.md)** are stream-based classes that provide fast, forward-only access to XML data. They're memory-efficient for large files but limited in functionality for complex operations.

**[XMLDocument](./XMLDocument.md)** provides a complete DOM representation of the XML document, allowing full navigation and manipulation but consuming more memory.

**LINQ to XML** offers a modern, object-oriented approach that balances efficiency with ease of use, making it ideal for most CRUD scenarios.

## CRUD Operations Implementation

LINQ to XML will likely be the most practical choice for implementing CRUD operations.

### **Create (Adding New Records)**

```csharp
// Create a new employee and add to existing XML
var xml = XElement.Load("empleats.xml");
var newEmpleat = new XElement("empleat",
    new XElement("id", 3),
    new XElement("nom", "Maria"),
    new XElement("cognom", "López"),
    new XElement("edat", 28)
);
xml.Add(newEmpleat);
xml.Save("empleats.xml");
```


### **Read (Querying Data)**

```csharp
// Read all employees
var xml = XElement.Load("empleats.xml");
var empleats = xml.Elements("empleat")
                  .Select(e => new
                  {
                      Id = int.Parse(e.Element("id").Value),
                      Nom = e.Element("nom").Value,
                      Cognom = e.Element("cognom").Value,
                      Edat = int.Parse(e.Element("edat").Value)
                  });

// Read with filtering (like your Ex.18)
var empleatsMajors = xml.Elements("empleat")
                        .Where(e => int.Parse(e.Element("edat").Value) > 26);
```


### **Update (Modifying Existing Records)**

```csharp
// Update an employee's age by ID
var xml = XElement.Load("empleats.xml");
var empleat = xml.Elements("empleat")
                 .FirstOrDefault(e => int.Parse(e.Element("id").Value) == 1);

if (empleat != null)
{
    empleat.Element("edat").Value = "31";
    xml.Save("empleats.xml");
}
```


### **Delete (Removing Records)**

```csharp
// Delete an employee by ID
var xml = XElement.Load("empleats.xml");
var empleatToDelete = xml.Elements("empleat")
                         .FirstOrDefault(e => int.Parse(e.Element("id").Value) == 2);

if (empleatToDelete != null)
{
    empleatToDelete.Remove();
    xml.Save("empleats.xml");
}
```


## Complete CRUD Class Example

```csharp
using System;
using System.Linq;
using System.Xml.Linq;

public class EmpleatManager
{
    private string filePath;
    
    public EmpleatManager(string xmlFilePath)
    {
        filePath = xmlFilePath;
    }
    
    // CREATE
    public void AddEmpleat(int id, string nom, string cognom, int edat)
    {
        var xml = XElement.Load(filePath);
        var newEmpleat = new XElement("empleat",
            new XElement("id", id),
            new XElement("nom", nom),
            new XElement("cognom", cognom),
            new XElement("edat", edat)
        );
        xml.Add(newEmpleat);
        xml.Save(filePath);
    }
    
    // READ
    public void DisplayAllEmpleats()
    {
        var xml = XElement.Load(filePath);
        var empleats = xml.Elements("empleat");
        
        foreach (var emp in empleats)
        {
            Console.WriteLine($"ID: {emp.Element("id").Value}, " +
                            $"Nom: {emp.Element("nom").Value}, " +
                            $"Cognom: {emp.Element("cognom").Value}, " +
                            $"Edat: {emp.Element("edat").Value}");
        }
    }
    
    // UPDATE
    public bool UpdateEmpleatAge(int id, int newAge)
    {
        var xml = XElement.Load(filePath);
        var empleat = xml.Elements("empleat")
                         .FirstOrDefault(e => int.Parse(e.Element("id").Value) == id);
        
        if (empleat != null)
        {
            empleat.Element("edat").Value = newAge.ToString();
            xml.Save(filePath);
            return true;
        }
        return false;
    }
    
    // DELETE
    public bool DeleteEmpleat(int id)
    {
        var xml = XElement.Load(filePath);
        var empleat = xml.Elements("empleat")
                         .FirstOrDefault(e => int.Parse(e.Element("id").Value) == id);
        
        if (empleat != null)
        {
            empleat.Remove();
            xml.Save(filePath);
            return true;
        }
        return false;
    }
}
```


## Key Tips

**Error Handling**: Always check if elements exist before accessing their values to avoid null reference exceptions.

**File Structure**: Ensure your XML file has a proper root element structure like:

```xml
<empleats>
    <empleat>
        <id>1</id>
        <nom>Joan</nom>
        <cognom>Garcia</cognom>
        <edat>30</edat>
    </empleat>
</empleats>
```

**LINQ Queries**: Practice combining `Where()`, `Select()`, `FirstOrDefault()`, and `OrderBy()` methods for complex data retrieval.

**Memory Management**: Remember that LINQ to XML loads the entire document into memory, so it's suitable for small to medium-sized files but consider [XMLReader](./XML-RW.md) for very large files.
