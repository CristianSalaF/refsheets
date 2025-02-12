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
The `@Preview` annotation allows you to see a preview of your composable function directly in the IDE without running the app on a device or emulator. This helps in quickly iterating UI designs.
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
  val context = LocalContext.current
  Toast.makeText(context, "Message", Toast.LENGTH_SHORT).show()
  ```

### **Buttons**
- **Basic Button:**
  ```kotlin
  Button(onClick = { /*TODO*/ }) {
      Text("Click Me")
  }
  ```
- **Outlined Button:**
  ```kotlin
  OutlinedButton(onClick = { /*TODO*/ }) {
      Text("Outlined")
  }
  ```
- **Text Button:**
  ```kotlin
  TextButton(onClick = { /*TODO*/ }) {
      Text("Text Button")
  }
  ```
- **Icon Button:**
  ```kotlin
  IconButton(onClick = { /*TODO*/ }) {
      Icon(Icons.Filled.Favorite, contentDescription = "Favorite")
  }
  ```

### **Text Components**
- **Basic Text:**
  ```kotlin
  Text(text = "Hello World")
  ```
- **Styled Text:**
  ```kotlin
  Text(
      text = "Styled Text",
      fontSize = 20.sp,
      fontWeight = FontWeight.Bold,
      color = Color.Blue
  )
  ```
- **Selectable Text:**
  ```kotlin
  SelectionContainer {
      Text("This text can be selected")
  }
  ```

### **Sliders**
- **Basic Slider:**
  ```kotlin
  var sliderPosition by remember { mutableStateOf(0f) }
  Slider(value = sliderPosition, onValueChange = { sliderPosition = it })
  ```
- **Range Slider:**
  ```kotlin
  var range by remember { mutableStateOf(0f..1f) }
  RangeSlider(
      values = range,
      onValueChange = { range = it }
  )
  ```

### **Progress Indicators**
- **Linear Progress Indicator:**
  ```kotlin
  LinearProgressIndicator(progress = 0.5f)
  ```
- **Circular Progress Indicator:**
  ```kotlin
  CircularProgressIndicator(progress = 0.7f)
  ```
- **Indeterminate Progress Indicator:**
  ```kotlin
  LinearProgressIndicator()
  CircularProgressIndicator()
  ```
- **Progress Bar with Countdown:**
  ```kotlin
  var progress by remember { mutableStateOf(1f) }
  LaunchedEffect(Unit) {
      while (progress > 0f) {
          delay(1000)
          progress -= 0.1f
      }
  }
  LinearProgressIndicator(progress = progress)
  ```

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
val visible by remember { mutableStateOf(true) }
val animatedAlpha by animateFloatAsState(targetValue = if (visible) 1f else 0f)
Box(Modifier.alpha(animatedAlpha))
```

### **Advanced Animations**
- Animating sizes:
  ```kotlin
  val expanded by remember { mutableStateOf(false) }
  val size by animateDpAsState(targetValue = if (expanded) 200.dp else 100.dp)
  Box(Modifier.size(size))
  ```

---

## **7. Lists and Lazy Components**

### **LazyColumn (Vertical List)**
```kotlin
val itemsList = listOf("Item 1", "Item 2", "Item 3")
LazyColumn {
    items(itemsList) { item ->
        Text(text = item)
    }
}
```

### **LazyRow (Horizontal List)**
```kotlin
val itemsList = listOf("Item 1", "Item 2", "Item 3")
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

## **9. Setting Up a Kotlin Compose Project**

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

