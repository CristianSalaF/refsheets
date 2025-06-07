## XMLDocument for XML Manipulation

XMLDocument loads the entire XML structure into memory as a tree, allowing you to navigate and modify any part of the document easily.

### **Loading and Reading with XMLDocument**

```csharp
using System;
using System.Xml;

public void ReadEmpleatsWithXmlDocument(string filePath)
{
    XmlDocument doc = new XmlDocument();
    doc.Load(filePath);
    
    // Get all empleat nodes
    XmlNodeList empleatNodes = doc.SelectNodes("//empleat");
    
    foreach (XmlNode empleat in empleatNodes)
    {
        string id = empleat.SelectSingleNode("id").InnerText;
        string nom = empleat.SelectSingleNode("nom").InnerText;
        string cognom = empleat.SelectSingleNode("cognom").InnerText;
        string edat = empleat.SelectSingleNode("edat").InnerText;
        
        Console.WriteLine($"ID: {id}, Nom: {nom}, Cognom: {cognom}, Edat: {edat}");
    }
}
```


### **Finding Specific Employee with XPath**

```csharp
public XmlNode FindEmpleatById(XmlDocument doc, int targetId)
{
    // Using XPath to find specific employee
    string xpath = $"//empleat[id='{targetId}']";
    return doc.SelectSingleNode(xpath);
}

public void DisplayEmpleatById(string filePath, int id)
{
    XmlDocument doc = new XmlDocument();
    doc.Load(filePath);
    
    XmlNode empleat = FindEmpleatById(doc, id);
    
    if (empleat != null)
    {
        Console.WriteLine($"Found: {empleat.SelectSingleNode("nom").InnerText} " +
                         $"{empleat.SelectSingleNode("cognom").InnerText}");
    }
    else
    {
        Console.WriteLine($"Employee with ID {id} not found.");
    }
}
```


### **Filtering with XPath Queries**

```csharp
public void FindEmpleatsByAge(string filePath, int minAge)
{
    XmlDocument doc = new XmlDocument();
    doc.Load(filePath);
    
    // XPath query to find employees older than specified age
    string xpath = $"//empleat[edat > {minAge}]";
    XmlNodeList empleats = doc.SelectNodes(xpath);
    
    Console.WriteLine($"Employees older than {minAge}:");
    foreach (XmlNode empleat in empleats)
    {
        string nom = empleat.SelectSingleNode("nom").InnerText;
        string cognom = empleat.SelectSingleNode("cognom").InnerText;
        string edat = empleat.SelectSingleNode("edat").InnerText;
        
        Console.WriteLine($"- {nom} {cognom} ({edat} years)");
    }
}
```


## Complete XMLDocument CRUD Implementation

```csharp
using System;
using System.Xml;

public class EmpleatManagerXmlDocument
{
    private string filePath;
    
    public EmpleatManagerXmlDocument(string xmlFilePath)
    {
        filePath = xmlFilePath;
    }
    
    // CREATE - Add new employee
    public void AddEmpleat(int id, string nom, string cognom, int edat)
    {
        XmlDocument doc = new XmlDocument();
        doc.Load(filePath);
        
        // Get root element
        XmlNode root = doc.DocumentElement;
        
        // Create new empleat element
        XmlElement newEmpleat = doc.CreateElement("empleat");
        
        // Create child elements
        XmlElement idElement = doc.CreateElement("id");
        idElement.InnerText = id.ToString();
        newEmpleat.AppendChild(idElement);
        
        XmlElement nomElement = doc.CreateElement("nom");
        nomElement.InnerText = nom;
        newEmpleat.AppendChild(nomElement);
        
        XmlElement cognomElement = doc.CreateElement("cognom");
        cognomElement.InnerText = cognom;
        newEmpleat.AppendChild(cognomElement);
        
        XmlElement edatElement = doc.CreateElement("edat");
        edatElement.InnerText = edat.ToString();
        newEmpleat.AppendChild(edatElement);
        
        // Add to root
        root.AppendChild(newEmpleat);
        
        // Save changes
        doc.Save(filePath);
    }
    
    // READ - Display all employees
    public void DisplayAllEmpleats()
    {
        XmlDocument doc = new XmlDocument();
        doc.Load(filePath);
        
        XmlNodeList empleats = doc.SelectNodes("//empleat");
        
        Console.WriteLine("All Employees:");
        foreach (XmlNode empleat in empleats)
        {
            DisplayEmpleat(empleat);
        }
    }
    
    // READ - Find employee by ID
    public void DisplayEmpleatById(int id)
    {
        XmlDocument doc = new XmlDocument();
        doc.Load(filePath);
        
        XmlNode empleat = doc.SelectSingleNode($"//empleat[id='{id}']");
        
        if (empleat != null)
        {
            Console.WriteLine($"Employee with ID {id}:");
            DisplayEmpleat(empleat);
        }
        else
        {
            Console.WriteLine($"Employee with ID {id} not found.");
        }
    }
    
    // UPDATE - Modify existing employee
    public bool UpdateEmpleat(int id, string newNom, string newCognom, int newEdat)
    {
        XmlDocument doc = new XmlDocument();
        doc.Load(filePath);
        
        XmlNode empleat = doc.SelectSingleNode($"//empleat[id='{id}']");
        
        if (empleat != null)
        {
            // Update individual fields
            if (empleat.SelectSingleNode("nom") != null)
                empleat.SelectSingleNode("nom").InnerText = newNom;
                
            if (empleat.SelectSingleNode("cognom") != null)
                empleat.SelectSingleNode("cognom").InnerText = newCognom;
                
            if (empleat.SelectSingleNode("edat") != null)
                empleat.SelectSingleNode("edat").InnerText = newEdat.ToString();
            
            doc.Save(filePath);
            return true;
        }
        
        return false;
    }
    
    // UPDATE - Modify specific field
    public bool UpdateEmpleatAge(int id, int newAge)
    {
        XmlDocument doc = new XmlDocument();
        doc.Load(filePath);
        
        XmlNode empleat = doc.SelectSingleNode($"//empleat[id='{id}']");
        
        if (empleat != null)
        {
            XmlNode edatNode = empleat.SelectSingleNode("edat");
            if (edatNode != null)
            {
                edatNode.InnerText = newAge.ToString();
                doc.Save(filePath);
                return true;
            }
        }
        
        return false;
    }
    
    // DELETE - Remove employee
    public bool DeleteEmpleat(int id)
    {
        XmlDocument doc = new XmlDocument();
        doc.Load(filePath);
        
        XmlNode empleat = doc.SelectSingleNode($"//empleat[id='{id}']");
        
        if (empleat != null)
        {
            empleat.ParentNode.RemoveChild(empleat);
            doc.Save(filePath);
            return true;
        }
        
        return false;
    }
    
    // Helper method to display employee information
    private void DisplayEmpleat(XmlNode empleat)
    {
        string id = empleat.SelectSingleNode("id")?.InnerText ?? "N/A";
        string nom = empleat.SelectSingleNode("nom")?.InnerText ?? "N/A";
        string cognom = empleat.SelectSingleNode("cognom")?.InnerText ?? "N/A";
        string edat = empleat.SelectSingleNode("edat")?.InnerText ?? "N/A";
        
        Console.WriteLine($"  ID: {id}, Nom: {nom}, Cognom: {cognom}, Edat: {edat}");
    }
}
```


## Advanced XMLDocument Features

### **Using XPath for Complex Queries**

```csharp
public void AdvancedQueries(string filePath)
{
    XmlDocument doc = new XmlDocument();
    doc.Load(filePath);
    
    // Find employees with names starting with 'A'
    XmlNodeList empleatsWithA = doc.SelectNodes("//empleat[starts-with(nom, 'A')]");
    
    // Find the oldest employee
    XmlNode oldestEmpleat = doc.SelectSingleNode("//empleat[not(../empleat/edat > edat)]");
    
    // Count employees by age range
    XmlNodeList youngEmployees = doc.SelectNodes("//empleat[edat < 30]");
    XmlNodeList seniorEmployees = doc.SelectNodes("//empleat[edat >= 30]");
    
    Console.WriteLine($"Young employees (< 30): {youngEmployees.Count}");
    Console.WriteLine($"Senior employees (>= 30): {seniorEmployees.Count}");
}
```


### **Working with Attributes**

```csharp
public void WorkWithAttributes(string filePath)
{
    XmlDocument doc = new XmlDocument();
    doc.Load(filePath);
    
    // Add attribute to an employee
    XmlNode empleat = doc.SelectSingleNode("//empleat[id='1']");
    if (empleat != null)
    {
        XmlAttribute statusAttr = doc.CreateAttribute("status");
        statusAttr.Value = "active";
        empleat.Attributes.Append(statusAttr);
        
        doc.Save(filePath);
    }
    
    // Read employees with specific attribute
    XmlNodeList activeEmployees = doc.SelectNodes("//empleat[@status='active']");
}
```


## Key XMLDocument Advantages

**Full DOM Access**: Unlike XMLReader's forward-only approach, XMLDocument allows you to navigate anywhere in the document tree.

**XPath Support**: Powerful query language for finding specific nodes with complex criteria.

**In-Place Modifications**: You can modify specific elements without rewriting the entire file.

**Node Manipulation**: Easy to add, remove, or modify individual nodes and their relationships.

**Attribute Handling**: Full support for XML attributes, which XMLReader handles differently.

XMLDocument is ideal when you need to perform complex manipulations, use XPath queries, or when the XML structure requires frequent random access to different parts of the document.