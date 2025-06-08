## Understanding Aggregation Pipeline Construction

Think of aggregation as a **factory assembly line** where data flows through different stations (stages), and each station transforms the data in a specific way.

### Basic Structure

```csharp
var pipeline = new[]
{
    new BsonDocument("$stage1", new BsonDocument { ... }),
    new BsonDocument("$stage2", new BsonDocument { ... }),
    new BsonDocument("$stage3", new BsonDocument { ... })
};

collection.Aggregate<BsonDocument>(pipeline)
```


## Step-by-Step Construction Guide

### Step 1: Start with Your Goal

**What do you want to achieve?** Let's say: "Count how many times each tag appears in the people collection"

### Step 2: Break It Down

1. **Split the tags array** (each person has multiple tags)
2. **Group by tag name** and count occurrences

### Step 3: Choose Your Stages

**`$unwind`** - Splits arrays into separate documents

```csharp
// Before: { name: "John", tags: ["sit", "esse", "dolore"] }
// After:  { name: "John", tags: "sit" }
//         { name: "John", tags: "esse" }  
//         { name: "John", tags: "dolore" }

new BsonDocument("$unwind", "$tags")
```

**`$group`** - Groups documents and performs calculations

```csharp
// Groups all documents with same tag and counts them
new BsonDocument("$group", new BsonDocument
{
    { "_id", "$tags" },                    // Group by tag value
    { "count", new BsonDocument("$sum", 1) }  // Count each occurrence
})
```


### Step 4: Put It Together

```csharp
public void CountTagOccurrences()
{
    var database = MongoConnection.GetDatabase("itb");
    var collection = database.GetCollection<BsonDocument>("people");
    
    var pipeline = new[]
    {
        // Stage 1: Split tags array
        new BsonDocument("$unwind", "$tags"),
        
        // Stage 2: Group by tag and count
        new BsonDocument("$group", new BsonDocument
        {
            { "_id", "$tags" },
            { "count", new BsonDocument("$sum", 1) }
        })
    };
    
    var results = collection.Aggregate<BsonDocument>(pipeline).ToList();
    foreach (var result in results)
    {
        Console.WriteLine($"Tag: {result["_id"]}, Count: {result["count"]}");
    }
}
```


## Common Aggregation Patterns

### Pattern 1: Filter → Group → Sort

```csharp
var pipeline = new[]
{
    // Filter: Only active people
    new BsonDocument("$match", new BsonDocument("isActive", true)),
    
    // Group: By gender, calculate average age
    new BsonDocument("$group", new BsonDocument
    {
        { "_id", "$gender" },
        { "avgAge", new BsonDocument("$avg", "$age") }
    }),
    
    // Sort: By average age descending
    new BsonDocument("$sort", new BsonDocument("avgAge", -1))
};
```


### Pattern 2: Unwind → Group → Collect Unique

```csharp
var pipeline = new[]
{
    // Split friends array
    new BsonDocument("$unwind", "$friends"),
    
    // Group by friend name (removes duplicates)
    new BsonDocument("$group", new BsonDocument
    {
        { "_id", "$friends.name" }
    }),
    
    // Sort alphabetically
    new BsonDocument("$sort", new BsonDocument("_id", 1))
};
```


## Key Construction Rules

### 1. **Field References**

- Use `"$fieldName"` to reference fields
- Use `"$subdocument.field"` for nested fields
- Example: `"$address.zipcode"`, `"$friends.name"`


### 2. **Operators Always Start with \$**

- `$sum`, `$avg`, `$max`, `$min`, `$count`
- `$match`, `$group`, `$sort`, `$unwind`


### 3. **Group Structure**

```csharp
new BsonDocument("$group", new BsonDocument
{
    { "_id", "what_to_group_by" },           // Required
    { "newField1", new BsonDocument("$sum", 1) },      // Optional calculations
    { "newField2", new BsonDocument("$avg", "$age") }  // More calculations
})
```


### 4. **Common Accumulator Operators**

- `$sum` - Add values: `new BsonDocument("$sum", "$price")` or `new BsonDocument("$sum", 1)` for counting
- `$avg` - Average: `new BsonDocument("$avg", "$age")`
- `$max` - Maximum: `new BsonDocument("$max", "$score")`
- `$min` - Minimum: `new BsonDocument("$min", "$score")`
- `$addToSet` - Unique values: `new BsonDocument("$addToSet", "$street")`


## Practical Exercise: Build This Step by Step

**Goal:** Show streets by zipcode from restaurants

**Step 1:** What do I want?

- Group restaurants by zipcode
- Collect unique street names for each zipcode

**Step 2:** Choose stages

- `$group` to group by zipcode
- `$addToSet` to collect unique streets

**Step 3:** Construct

```csharp
var pipeline = new[]
{
    new BsonDocument("$group", new BsonDocument
    {
        { "_id", "$address.zipcode" },                                    // Group by zipcode
        { "streets", new BsonDocument("$addToSet", "$address.street") }   // Collect unique streets
    })
};
```


## Debugging Tips

### 1. **Test Each Stage Separately**

```csharp
// Test just the first stage
var pipeline1 = new[] { new BsonDocument("$unwind", "$tags") };
var results1 = collection.Aggregate<BsonDocument>(pipeline1).ToList();

// Then add the second stage
var pipeline2 = new[]
{
    new BsonDocument("$unwind", "$tags"),
    new BsonDocument("$group", new BsonDocument { { "_id", "$tags" }, { "count", new BsonDocument("$sum", 1) } })
};
```


### 2. **Use Limit for Testing**

```csharp
var pipeline = new[]
{
    new BsonDocument("$limit", 5),  // Only process 5 documents
    new BsonDocument("$unwind", "$tags")
};
```

The key is to **think in stages** and **build incrementally**. Start simple, test each stage, then add complexity!