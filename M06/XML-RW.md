## XMLReader for Reading XML Data

XMLReader provides fast, forward-only access to XML data with minimal memory usage. Here are practical examples for CRUD operations:

### **Reading All Employees with XMLReader**

```csharp
using System;
using System.Xml;

public void ReadEmpleatsWithXmlReader(string filePath)
{
    using (XmlReader reader = XmlReader.Create(filePath))
    {
        while (reader.Read())
        {
            if (reader.NodeType == XmlNodeType.Element && reader.Name == "empleat")
            {
                // Read the empleat element and its children
                XmlReader empleatReader = reader.ReadSubtree();
                ReadSingleEmpleat(empleatReader);
            }
        }
    }
}

private void ReadSingleEmpleat(XmlReader reader)
{
    string id = "", nom = "", cognom = "", edat = "";
    
    while (reader.Read())
    {
        if (reader.NodeType == XmlNodeType.Element)
        {
            switch (reader.Name)
            {
                case "id":
                    id = reader.ReadElementContentAsString();
                    break;
                case "nom":
                    nom = reader.ReadElementContentAsString();
                    break;
                case "cognom":
                    cognom = reader.ReadElementContentAsString();
                    break;
                case "edat":
                    edat = reader.ReadElementContentAsString();
                    break;
            }
        }
    }
    
    Console.WriteLine($"ID: {id}, Nom: {nom}, Cognom: {cognom}, Edat: {edat}");
}
```


### **Finding Specific Employee with XMLReader**

```csharp
public bool FindEmpleatById(string filePath, int targetId)
{
    using (XmlReader reader = XmlReader.Create(filePath))
    {
        while (reader.Read())
        {
            if (reader.NodeType == XmlNodeType.Element && reader.Name == "empleat")
            {
                XmlReader empleatReader = reader.ReadSubtree();
                empleatReader.Read(); // Move to empleat element
                
                while (empleatReader.Read())
                {
                    if (empleatReader.NodeType == XmlNodeType.Element && empleatReader.Name == "id")
                    {
                        int currentId = empleatReader.ReadElementContentAsInt();
                        if (currentId == targetId)
                        {
                            Console.WriteLine($"Found employee with ID: {targetId}");
                            return true;
                        }
                        break; // Move to next empleat
                    }
                }
            }
        }
    }
    return false;
}
```


## XMLWriter for Creating XML Data

XMLWriter allows you to generate XML streams efficiently:

### **Creating New XML File with XMLWriter**

```csharp
using System.Xml;

public void CreateEmpleatsFileWithXmlWriter(string filePath)
{
    XmlWriterSettings settings = new XmlWriterSettings
    {
        Indent = true,
        IndentChars = "  "
    };
    
    using (XmlWriter writer = XmlWriter.Create(filePath, settings))
    {
        writer.WriteStartDocument();
        writer.WriteStartElement("empleats");
        
        // Add first employee
        WriteEmpleat(writer, 1, "Joan", "Garcia", 30);
        
        // Add second employee
        WriteEmpleat(writer, 2, "Anna", "Martínez", 25);
        
        writer.WriteEndElement(); // Close empleats
        writer.WriteEndDocument();
    }
}

private void WriteEmpleat(XmlWriter writer, int id, string nom, string cognom, int edat)
{
    writer.WriteStartElement("empleat");
    writer.WriteElementString("id", id.ToString());
    writer.WriteElementString("nom", nom);
    writer.WriteElementString("cognom", cognom);
    writer.WriteElementString("edat", edat.ToString());
    writer.WriteEndElement();
}
```


### **Appending New Employee with XMLWriter**

```csharp
public void AddEmpleatWithXmlWriter(string filePath, int id, string nom, string cognom, int edat)
{
    // First, read existing data
    List<Employee> existingEmployees = ReadAllEmployeesWithXmlReader(filePath);
    
    // Add new employee
    existingEmployees.Add(new Employee { Id = id, Nom = nom, Cognom = cognom, Edat = edat });
    
    // Rewrite entire file
    XmlWriterSettings settings = new XmlWriterSettings
    {
        Indent = true,
        IndentChars = "  "
    };
    
    using (XmlWriter writer = XmlWriter.Create(filePath, settings))
    {
        writer.WriteStartDocument();
        writer.WriteStartElement("empleats");
        
        foreach (var emp in existingEmployees)
        {
            WriteEmpleat(writer, emp.Id, emp.Nom, emp.Cognom, emp.Edat);
        }
        
        writer.WriteEndElement();
        writer.WriteEndDocument();
    }
}

public class Employee
{
    public int Id { get; set; }
    public string Nom { get; set; }
    public string Cognom { get; set; }
    public int Edat { get; set; }
}
```


## Complete XMLReader/XMLWriter CRUD Example

```csharp
using System;
using System.Collections.Generic;
using System.Xml;

public class EmpleatManagerXmlReaderWriter
{
    private string filePath;
    
    public EmpleatManagerXmlReaderWriter(string xmlFilePath)
    {
        filePath = xmlFilePath;
    }
    
    // CREATE - Add new employee
    public void AddEmpleat(int id, string nom, string cognom, int edat)
    {
        var employees = ReadAllEmployees();
        employees.Add(new Employee { Id = id, Nom = nom, Cognom = cognom, Edat = edat });
        WriteAllEmployees(employees);
    }
    
    // READ - Get all employees
    public List<Employee> ReadAllEmployees()
    {
        var employees = new List<Employee>();
        
        using (XmlReader reader = XmlReader.Create(filePath))
        {
            while (reader.Read())
            {
                if (reader.NodeType == XmlNodeType.Element && reader.Name == "empleat")
                {
                    var employee = ReadEmployeeFromXml(reader.ReadSubtree());
                    if (employee != null)
                        employees.Add(employee);
                }
            }
        }
        
        return employees;
    }
    
    // UPDATE - Modify existing employee
    public bool UpdateEmpleat(int id, string newNom, string newCognom, int newEdat)
    {
        var employees = ReadAllEmployees();
        var employee = employees.Find(e => e.Id == id);
        
        if (employee != null)
        {
            employee.Nom = newNom;
            employee.Cognom = newCognom;
            employee.Edat = newEdat;
            WriteAllEmployees(employees);
            return true;
        }
        
        return false;
    }
    
    // DELETE - Remove employee
    public bool DeleteEmpleat(int id)
    {
        var employees = ReadAllEmployees();
        var employee = employees.Find(e => e.Id == id);
        
        if (employee != null)
        {
            employees.Remove(employee);
            WriteAllEmployees(employees);
            return true;
        }
        
        return false;
    }
    
    private Employee ReadEmployeeFromXml(XmlReader reader)
    {
        var employee = new Employee();
        
        while (reader.Read())
        {
            if (reader.NodeType == XmlNodeType.Element)
            {
                switch (reader.Name)
                {
                    case "id":
                        employee.Id = reader.ReadElementContentAsInt();
                        break;
                    case "nom":
                        employee.Nom = reader.ReadElementContentAsString();
                        break;
                    case "cognom":
                        employee.Cognom = reader.ReadElementContentAsString();
                        break;
                    case "edat":
                        employee.Edat = reader.ReadElementContentAsInt();
                        break;
                }
            }
        }
        
        return employee;
    }
    
    private void WriteAllEmployees(List<Employee> employees)
    {
        XmlWriterSettings settings = new XmlWriterSettings
        {
            Indent = true,
            IndentChars = "  "
        };
        
        using (XmlWriter writer = XmlWriter.Create(filePath, settings))
        {
            writer.WriteStartDocument();
            writer.WriteStartElement("empleats");
            
            foreach (var emp in employees)
            {
                writer.WriteStartElement("empleat");
                writer.WriteElementString("id", emp.Id.ToString());
                writer.WriteElementString("nom", emp.Nom);
                writer.WriteElementString("cognom", emp.Cognom);
                writer.WriteElementString("edat", emp.Edat.ToString());
                writer.WriteEndElement();
            }
            
            writer.WriteEndElement();
            writer.WriteEndDocument();
        }
    }
}
```


## Key Differences
**XMLReader/XMLWriter Approach:**

- More verbose code but better performance for large files
- Forward-only reading means you need to process data sequentially
- Updates require reading all data and rewriting the entire file
- Better memory efficiency for large datasets

**LINQ to XML Approach:**

- More concise and readable code
- Allows random access to any element
- Can modify specific elements without rewriting entire file
- Easier to work with but uses more memory

