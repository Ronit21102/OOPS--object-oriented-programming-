# OOPS--object-oriented-programming-
### **What is `finalize()` in Java?**  
The `finalize()` method in Java is a **special method that gets called before an object is garbage collected**. It was used to **perform cleanup operations** like closing resources before the object is destroyed.

📌 **However, `finalize()` is deprecated in Java 9** and later because it is **unreliable** and may cause performance issues.

---

## **1️⃣ Syntax of `finalize()`**
```java
class Example {
    @Override
    protected void finalize() throws Throwable {
        System.out.println("Finalize method called before garbage collection");
    }
}
```
- **`finalize()` is defined in `Object` class** and can be overridden.
- It is called **before the object is garbage collected**.
- It is **not guaranteed** to run immediately.

---

## **2️⃣ Example: How `finalize()` Works**
```java
class Example {
    int id;
    
    Example(int id) {
        this.id = id;
    }

    @Override
    protected void finalize() throws Throwable {
        System.out.println("Object with ID " + id + " is being garbage collected");
    }

    public static void main(String[] args) {
        Example obj1 = new Example(1);
        Example obj2 = new Example(2);

        obj1 = null; // Eligible for garbage collection
        obj2 = null; // Eligible for garbage collection

        System.gc(); // Request garbage collection (not guaranteed)

        System.out.println("End of program");
    }
}
```
### **🔹 Possible Output:**
```
End of program
Object with ID 1 is being garbage collected
Object with ID 2 is being garbage collected
```
📌 **Garbage collection is not immediate**. It runs when the JVM decides.

---

## **3️⃣ Why `finalize()` is Deprecated?**
1. **Unreliable Execution** → JVM may delay or never call `finalize()`.
2. **Performance Issues** → Garbage collection is slower if `finalize()` is used.
3. **Better Alternatives Exist** → Use **try-with-resources** (`AutoCloseable`) instead.

---

## **4️⃣ Alternative to `finalize()`: `AutoCloseable`**
Instead of `finalize()`, use **`try-with-resources` and `AutoCloseable`**:
```java
class Resource implements AutoCloseable {
    public void use() {
        System.out.println("Using resource...");
    }

    @Override
    public void close() {
        System.out.println("Resource closed.");
    }
}

public class Main {
    public static void main(String[] args) {
        try (Resource res = new Resource()) {
            res.use();
        } // ✅ `close()` is automatically called here
    }
}
```
### **🔹 Output:**
```
Using resource...
Resource closed.
```
📌 **This is a safer and more efficient way to clean up resources!** 🚀

---

## **🚀 Summary**
| Feature | `finalize()` | `AutoCloseable` (`try-with-resources`) |
|---------|------------|----------------|
| **Purpose** | Cleanup before GC | Cleanup resources immediately |
| **When Called?** | Before GC (not guaranteed) | At the end of `try` block |
| **Performance** | Slow (affects GC) | Fast and efficient |
| **Recommended?** | ❌ Deprecated (Java 9+) | ✅ Yes (Use `close()`) |

### **`final` Keyword in Java**  

The `final` keyword in Java is used to **restrict modification** in three different contexts:  
1. **Final Variable** → Value **cannot change** after assignment.  
2. **Final Method** → Method **cannot be overridden** in subclasses.  
3. **Final Class** → Class **cannot be inherited (extended)**.  

---

## **1️⃣ `final` Variable (Constant)**
A `final` variable is **a constant whose value cannot be changed after assignment**.

### **Example: `final` with Variables**
```java
class Example {
    final int MAX_SPEED = 120; // Constant value

    void show() {
        // MAX_SPEED = 150; // ❌ Error: Cannot reassign a final variable
        System.out.println("Max Speed: " + MAX_SPEED);
    }
}
```
✔ **Once assigned, `MAX_SPEED` cannot be changed.**  
✔ **Useful for defining constants (e.g., `final double PI = 3.14159;`)**.

---

## **2️⃣ `final` Method (Prevents Overriding)**
A `final` method **cannot be overridden** by subclasses.

### **Example: `final` Method**
```java
class Parent {
    final void display() {
        System.out.println("This is a final method.");
    }
}

class Child extends Parent {
    // void display() { ❌ Error: Cannot override final method
    //    System.out.println("Trying to override");
    // }
}
```
✔ **Ensures that a method’s implementation remains unchanged in subclasses.**

---

## **3️⃣ `final` Class (Prevents Inheritance)**
A `final` class **cannot be extended** (subclassed).

### **Example: `final` Class**
```java
final class Animal {
    void sound() {
        System.out.println("Animal makes a sound.");
    }
}

// class Dog extends Animal { ❌ Error: Cannot inherit from a final class
// }
```
✔ **Useful for security and utility classes (e.g., `java.lang.String` is final).**

---

## **4️⃣ `final` with Objects (Reference vs. Value)**
A `final` object **prevents reassigning the reference, but allows modifying the object's data**.

### **Example: Final Reference vs. Final Value**
```java
class Car {
    String model;
    Car(String model) {
        this.model = model;
    }
}

public class Main {
    public static void main(String[] args) {
        final Car myCar = new Car("Tesla"); // Reference is final

        myCar.model = "BMW"; // ✅ Allowed (Modifying object)
        // myCar = new Car("Audi"); // ❌ Error: Cannot reassign a final reference

        System.out.println(myCar.model); // ✅ "BMW"
    }
}
```
✔ **You can modify the object's properties, but you cannot assign a new object to `myCar`.**

---

## **🚀 Summary Table**
| Feature | Description | Example |
|---------|------------|---------|
| **Final Variable** | Value **cannot change** after assignment. | `final int MAX_SPEED = 120;` |
| **Final Method** | **Cannot be overridden** by subclasses. | `final void show() {}` |
| **Final Class** | **Cannot be extended** by subclasses. | `final class Animal {}` |
| **Final Object** | **Reference cannot change**, but object properties can. | `final Car myCar = new Car("Tesla");` |

### **Heap vs. Stack Memory: In-Depth Explanation**  

JavaScript (and TypeScript) use **two main types of memory storage**:  
- **Stack Memory** (for primitives and function execution)  
- **Heap Memory** (for reference types like objects and arrays)

Understanding **how memory works** is essential for performance optimization, debugging, and avoiding memory leaks.

---

## **1️⃣ What is Stack Memory?**  
The **stack** is a **fast, linear** memory structure that stores:
- **Primitive data types** (`string`, `number`, `boolean`, etc.).
- **Function calls and execution context**.
- **Local variables and function arguments**.

📌 **Stack works in a LIFO (Last In, First Out) manner.**  

### **🔹 Example of Stack Memory Usage**
```ts
function multiply(x: number, y: number): number {
  let result = x * y; // Stored in stack
  return result;
}

let num1 = 10; // Stored in stack
let num2 = 20; // Stored in stack
let product = multiply(num1, num2); // Function call added to stack
```
🔹 **How it works internally in Stack Memory:**
```
Stack:
1. num1 = 10
2. num2 = 20
3. multiply(10, 20) → result = 200
4. remove multiply() from stack after execution
```
✔ **Stack is automatically managed** and cleaned up when a function returns.

### **Key Properties of Stack Memory:**
| Feature | Stack Memory |
|---------|--------------|
| **Speed** | Very fast (direct memory allocation) |
| **Size** | Small (limited per thread) |
| **Type of Data** | Primitives, function calls, execution context |
| **Lifetime** | Data is automatically removed when out of scope |
| **Access** | Direct, sequential access (LIFO) |

---

## **2️⃣ What is Heap Memory?**  
The **heap** is a **dynamic, unordered memory region** used for:
- **Non-primitive (Reference) types** → Objects, Arrays, Functions, Classes.
- **Large data structures**.
- **Global variables that persist longer**.

📌 **Heap is not automatically cleaned up; JavaScript uses** **Garbage Collection** **to free memory.**

### **🔹 Example of Heap Memory Usage**
```ts
let person1 = { name: "Alice", age: 25 };
let person2 = person1; // Both reference the same heap object

person2.age = 30; 
console.log(person1.age); // ✅ 30 (because person1 and person2 point to the same object)
```
🔹 **How it works internally in Heap Memory:**
```
Stack:
1. person1 → points to heap memory
2. person2 → also points to heap memory

Heap:
1. { name: "Alice", age: 25 } → modified to { name: "Alice", age: 30 }
```
✔ **Heap stores objects globally** and **must be cleaned manually** (via Garbage Collection).

### **Key Properties of Heap Memory:**
| Feature | Heap Memory |
|---------|-------------|
| **Speed** | Slower than Stack (requires lookup) |
| **Size** | Large (used for dynamic allocations) |
| **Type of Data** | Objects, arrays, functions, closures |
| **Lifetime** | Data persists until garbage collection removes it |
| **Access** | Random access (not sequential) |

---

## **3️⃣ Key Differences Between Stack and Heap**
| Feature | **Stack Memory** | **Heap Memory** |
|---------|----------------|----------------|
| **Type of Data** | Stores **primitives** & function calls | Stores **objects, arrays, functions** |
| **Speed** | 🚀 **Fast (direct memory allocation)** | 🐢 **Slower (dynamic memory allocation)** |
| **Access** | **LIFO (Last In, First Out)** | **Unstructured, allocated dynamically** |
| **Lifetime** | **Automatically removed** after function execution | **Stays until garbage collection** removes it |
| **Size** | **Smaller** (per thread) | **Larger** (shared memory) |
| **Example** | `let a = 10;` | `let obj = { name: "John" };` |

---

## **4️⃣ Real-World Example: Stack vs. Heap**
### **🔹 Stack Example (Primitives)**
```ts
let a = 10;
let b = a; // Copying value (stored separately)
b = 20;
console.log(a); // ✅ 10 (unchanged, because stack stores values separately)
```
✔ **Stack stores separate copies of values**.

---

### **🔹 Heap Example (Objects & References)**
```ts
let obj1 = { name: "Alice" };
let obj2 = obj1; // Both point to the same object in the heap

obj2.name = "Bob";
console.log(obj1.name); // ✅ "Bob" (modified because both reference the same object)
```
✔ **Heap stores references, not copies**.

---

## **5️⃣ What Happens When Memory is Not Managed? (Memory Leaks)**
If an object remains **referenced but is no longer needed**, it stays in the heap **forever**, causing a **memory leak**.

### **Example of a Memory Leak**
```ts
let cache = {};
function storeData(key, value) {
  cache[key] = value; // Stored in heap, never released
}
storeData("user", { name: "John" });
```
✔ **Since `cache` never clears, data keeps growing indefinitely**.  
✔ Use **WeakMap, manual cleanup, or object reassignment** to prevent leaks.

---

## **🚀 Summary**
| **Concept** | **Stack Memory** | **Heap Memory** |
|------------|----------------|----------------|
| **Stores** | Primitives, function calls | Objects, arrays, functions |
| **Speed** | 🚀 Fast (LIFO order) | 🐢 Slower (random access) |
| **Size** | Small | Large |
| **Data Removal** | Automatically removed after function execution | Stays until garbage collection runs |
| **Mutability** | Immutable | Mutable |
| **Example** | `let x = 10;` | `let obj = { name: "John" };` |

---

## **6️⃣ Which One Should You Use?**
| **Use Stack When…** | **Use Heap When…** |
|-----------------|----------------|
| Storing **primitives** (strings, numbers, booleans) | Storing **objects, arrays, or functions** |
| Need **fast performance** | Need **large or dynamic storage** |
| Function **call execution** | Long-lived data like **caching, global objects** |

---

### **🔹 Final Thought**
- Stack is **fast**, **efficient**, and **automatically cleaned**.
- Heap is **larger**, **flexible**, and **requires garbage collection**.
- Understanding **stack vs. heap** helps optimize memory usage, avoid performance issues, and prevent memory leaks.

Would you like **examples of memory optimization** for real-world apps? 🚀😊
