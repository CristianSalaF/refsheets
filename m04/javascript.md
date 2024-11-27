# JavaScript essentials - quickref

[Go back](../README.md)

---

### **1. Selecting and Manipulating DOM Elements**
- **Selectors**:
  - `const element = document.getElementById("id");`
  - `const elements = document.getElementsByClassName("class");`
  - `const element = document.querySelector("selector");` (e.g., `"#id"`, `".class"`)
  - `const elements = document.querySelectorAll("selector");`
- **Manipulating Content**:
  - `element.textContent = "New text";` (Changes text content)
  - `element.innerHTML = "<b>Bold text</b>";` (Changes HTML content)
  - `element.style.property = "value";` (e.g., `element.style.color = "red";`)

---

### **2. Variable Declaration**
- Use `let` and `const`:
  - `let` for variables that can change.
  - `const` for variables that shouldn’t be reassigned.
- **Example**:
  ```javascript
  let count = 0;
  const max = 10;
  ```

---

### **3. Adding Event Listeners**
- General Syntax:
  ```javascript
  item.addEventListener("eventType", functionName);
  ```
- Example:
  ```javascript
  button.addEventListener("click", () => {
      alert("Button clicked!");
  });
  ```
- Common Event Types:
  - `"click"`, `"mouseover"`, `"keydown"`, `"submit"`, `"input"`

---

### **4. Functions**
- **Declaration**:
  ```javascript
  function greet() {
      console.log("Hello!");
  }
  ```
- **Arrow Function**:
  ```javascript
  const greet = () => console.log("Hello!");
  ```

---

### **5. Conditionals and Loops**
- **Conditionals**:
  ```javascript
  if (condition) {
      // code
  } else if (otherCondition) {
      // code
  } else {
      // code
  }
  ```
- **Loops**:
  - `for` loop:
    ```javascript
    for (let i = 0; i < 10; i++) {
        console.log(i);
    }
    ```
  - `for...of` (for arrays):
    ```javascript
    for (const item of array) {
        console.log(item);
    }
    ```
  - `for...in` (for object properties):
    ```javascript
    for (const key in object) {
        console.log(key, object[key]);
    }
    ```
  - `while` loop:
    ```javascript
    while (condition) {
        // code
    }
    ```

---

### **6. Arrays and Objects**
- **Arrays**:
  ```javascript
  const fruits = ["apple", "banana", "cherry"];
  fruits.push("orange"); // Add to end
  fruits.pop(); // Remove last
  fruits[0]; // Access first item
  ```
- **Objects**:
  ```javascript
  const person = { name: "John", age: 30 };
  person.name; // Access property
  person["age"]; // Alternative access
  ```

---

### **7. Common Methods**
- **String**:
  - `.toUpperCase()`, `.toLowerCase()`
  - `.includes("text")`
  - `.slice(start, end)`
- **Array**:
  - `.map()`, `.filter()`, `.reduce()`
  - `.forEach()`
  - `.find()`, `.indexOf()`
- **Object**:
  - `Object.keys(obj)`, `Object.values(obj)`, `Object.entries(obj)`

---

### **8. Working with Timers**
- **Delay Code**:
  ```javascript
  setTimeout(() => {
      console.log("Runs after 1 second");
  }, 1000);
  ```
- **Repeating Code**:
  ```javascript
  setInterval(() => {
      console.log("Runs every 1 second");
  }, 1000);
  ```

---

### **9. Best Practices**
- Always declare variables with `let` or `const` (avoid `var`).
- Use `===` for strict equality checks.
- Avoid global variables; encapsulate logic in functions or blocks.
- Use descriptive variable and function names.

---

### **10. Debugging**
- Use `console.log(variable)` to inspect values.
- Use the browser's Developer Tools (F12) to view errors and debug.

---
[Go back](../README.md)
