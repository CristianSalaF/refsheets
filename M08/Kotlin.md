# Kotlin Compose Reference Guide

This guide is structured to provide a comprehensive reference for Kotlin Compose in Gradle projects, from basic to advanced topics, grouped by feature types.

---

## **1. Basics of Kotlin**

### **Variable Types**
- **Immutable (val):**
  ```kotlin
  val name: String = "John"
  ```
- **Mutable (var):**
  ```kotlin
  var age: Int = 25
  ```

### **Functions**
```kotlin
fun greet(name: String): String = "Hello, $name"
fun add(a: Int, b: Int): Int = a + b
```

### **Lambdas**
```kotlin
val multiply: (Int, Int) -> Int = { a, b -> a * b }
```

---

## **2. Setting Up a Gradle Project**

Add the following Compose dependencies to `build.gradle.kts`:
```kotlin
dependencies {
    implementation("androidx.compose.ui:ui:1.5.0")
    implementation("androidx.compose.material:material:1.5.0")
    implementation("androidx.compose.ui:ui-tooling-preview:1.5.0")
    implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.6.0")
}
```

---

## **3. Compose Basics**

### **Composable Functions**
- Basic composable function:
  ```kotlin
  @Composable
  fun Greeting(name: String) {
      Text(text = "Hello, $name!")
  }
  ```

### **Preview**
```kotlin
@Preview(showBackground = true)
@Composable
fun PreviewGreeting() {
    Greeting(name = "Compose")
}
```

---

## **4. Compose UI Components**

### **Containers**

- **Column**: Arranges children vertically.
  ```kotlin
  Column {
      Text("Item 1")
      Text("Item 2")
  }
  ```
- **Row**: Arranges children horizontally.
  ```kotlin
  Row {
      Text("Item 1")
      Text("Item 2")
  }
  ```
- **Box**: Stacks children.
  ```kotlin
  Box {
      Text("On top")
      Image(painter = painterResource(R.drawable.image), contentDescription = null)
  }
  ```

### **Scaffold**
- Used for layouts with top bars, bottom bars, etc.
  ```kotlin
  @Composable
  fun MyScaffold() {
      Scaffold(
          topBar = {
              TopAppBar(title = { Text("My App") })
          },
          floatingActionButton = {
              FloatingActionButton(onClick = { /*TODO*/ }) {
                  Icon(Icons.Filled.Add, contentDescription = null)
              }
          }
      ) {
          Text("Content here")
      }
  }
  ```

### **Toast**
- Displaying a toast:
  ```kotlin
  Toast.makeText(context, "Message", Toast.LENGTH_SHORT).show()
  ```
  Use `LocalContext.current` to access context in Compose.

---

## **5. State Management**

### **MutableStateOf**
```kotlin
var count by remember { mutableStateOf(0) }
Button(onClick = { count++ }) {
    Text("Count: $count")
}
```

### **Remember**
- Retains state across recompositions.
  ```kotlin
  val state = remember { mutableStateOf("Hello") }
  ```

### **DerivedStateOf**
- Computes derived state efficiently.
  ```kotlin
  val derivedState = derivedStateOf { count * 2 }
  ```

---

## **6. Animations**

### **Basic Animation**
```kotlin
val animatedAlpha by animateFloatAsState(targetValue = if (visible) 1f else 0f)
Box(Modifier.alpha(animatedAlpha))
```

### **Advanced Animations**
- Animating sizes:
  ```kotlin
  val size by animateDpAsState(targetValue = if (expanded) 200.dp else 100.dp)
  Box(Modifier.size(size))
  ```

---

## **7. Lists and Lazy Components**

### **LazyColumn (Vertical List)**
```kotlin
LazyColumn {
    items(itemsList) { item ->
        Text(text = item)
    }
}
```

### **LazyRow (Horizontal List)**
```kotlin
LazyRow {
    items(itemsList) { item ->
        Text(text = item)
    }
}
```

---

## **8. Navigation**

### **Adding Navigation**
```kotlin
val navController = rememberNavController()
NavHost(navController, startDestination = "screen1") {
    composable("screen1") { Screen1(navController) }
    composable("screen2") { Screen2() }
}
```

### **Navigate Between Screens**
```kotlin
Button(onClick = { navController.navigate("screen2") }) {
    Text("Go to Screen 2")
}
```

---

## **9. Testing Compose**

### **Compose Test Dependencies**
```kotlin
androidTestImplementation("androidx.compose.ui:ui-test-junit4:1.5.0")
```

### **Write UI Tests**
```kotlin
composeTestRule.setContent {
    Greeting(name = "Test")
}
composeTestRule.onNodeWithText("Hello, Test!").assertExists()
```

---

## **10. Setting Up a Kotlin Compose Project**

### **Project Structure**
When creating a Kotlin Compose project, ensure your project contains the following key folders:

- **`src/main/java`**: Contains all Kotlin source files.
- **`src/main/res/drawable`**: Stores drawable resources like images.
- **`src/main/res/layout`**: Typically used for XML layouts (less relevant for Compose).
- **`src/main/res/values`**: Contains XML files for strings, colors, and dimensions.

### **Using IntelliJ IDEA Ultimate**
1. Create a new project.
2. Select **Kotlin Multiplatform** or **Kotlin Android**.
3. Include Compose dependencies in `build.gradle.kts`.
4. Define your `MainActivity`:
   ```kotlin
   class MainActivity : ComponentActivity() {
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContent {
               MyApp()
           }
       }
   }
   ```

### **Other IDEs**
For other IDEs like Android Studio:
1. Select **Empty Compose Activity** when creating a project.
2. Add necessary dependencies to `build.gradle` or `build.gradle.kts`.
3. Follow the same project structure as outlined above.