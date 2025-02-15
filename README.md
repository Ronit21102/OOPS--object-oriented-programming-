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

Yes, in Object-Oriented Programming (OOP) in Java, the **type of reference variable** determines **which members (methods and fields) can be accessed**, **not the actual object type** that was instantiated.  

### **Key Concept: Reference Type vs. Object Type**  
In Java, when you create an object, it has:  
1. **Reference Type** → The declared type of the variable (compile-time type).  
2. **Object Type** → The actual type of the object created at runtime.  

The reference type controls **which members are accessible** at **compile time**. The actual object type determines **which methods are executed** at **runtime (due to method overriding and polymorphism).**  

---

### **Example 1: Reference Type Controls Member Access**
```java
class Parent {
    void show() {
        System.out.println("Parent show()");
    }
}

class Child extends Parent {
    void display() {
        System.out.println("Child display()");
    }
}

public class Main {
    public static void main(String[] args) {
        Parent obj = new Child(); // Reference type: Parent, Object type: Child
        obj.show();   // ✅ Allowed (because 'show()' exists in Parent)
        // obj.display(); // ❌ Compile-time error (because Parent doesn't have 'display()')
    }
}
```
- Here, `obj` is declared as `Parent`, so only members of `Parent` can be accessed.
- Even though the actual object is `Child`, we **cannot** access `display()` because it's not in `Parent`.

---

### **Example 2: Runtime Method Execution (Method Overriding)**
```java
class Parent {
    void show() {
        System.out.println("Parent show()");
    }
}

class Child extends Parent {
    @Override
    void show() {
        System.out.println("Child show()");
    }
}

public class Main {
    public static void main(String[] args) {
        Parent obj = new Child(); // Reference type: Parent, Object type: Child
        obj.show();  // Outputs: "Child show()" (Method overriding applies)
    }
}
```
- Even though `obj` is of type `Parent`, the `show()` method of `Child` is executed due to **runtime polymorphism** (dynamic method dispatch).

---

### **Key Takeaways**
1. **The reference type determines what members (fields/methods) can be accessed.**
2. **Method calls are resolved at runtime (dynamic method dispatch) if overridden.**
3. **Fields are resolved based on the reference type, not the actual object type.**
4. **To access subclass-specific members, explicit type casting is required.**
   ```java
   ((Child) obj).display(); // ✅ Allowed after type casting
   ```

In Java, **multiple inheritance using classes is not allowed** to avoid the **"Diamond Problem."**  

### **Scenario: Two Parent Classes with Same Variable**
If Java allowed multiple inheritance like this:
```java
class Parent1 {
    int value = 10;
}

class Parent2 {
    int value = 20;
}

// ❌ Java does NOT allow this!
class Child extends Parent1, Parent2 {
    void printValue() {
        System.out.println(value); // Ambiguity: Which 'value' to use?
    }
}
```
👉 **Compilation Error:** Java does not allow this because it leads to ambiguity.  
If `Child` inherits from both `Parent1` and `Parent2`, and both define `value`, **Java wouldn't know which one to use.**

---

### **How Java Solves This: Using Interfaces**
Java allows multiple inheritance only through **interfaces** because interfaces **do not store state (no instance variables).**  
Example:
```java
interface Parent1 {
    int value1 = 10; // Static and final by default
}

interface Parent2 {
    int value2 = 20; // Static and final by default
}

class Child implements Parent1, Parent2 {
    void printValues() {
        System.out.println(Parent1.value1); // No ambiguity
        System.out.println(Parent2.value2);
    }
}

public class Main {
    public static void main(String[] args) {
        Child obj = new Child();
        obj.printValues();
    }
}
```
✔ **Works fine** because interfaces only define **constants (static & final variables),** avoiding ambiguity.

---

### **If You Really Need Shared Behavior? Use Composition**
Instead of multiple inheritance, Java uses **composition** (Has-A relationship):
```java
class Parent1 {
    int value = 10;
}

class Parent2 {
    int value = 20;
}

class Child {
    Parent1 p1 = new Parent1();
    Parent2 p2 = new Parent2();

    void printValues() {
        System.out.println(p1.value); // Access Parent1's variable
        System.out.println(p2.value); // Access Parent2's variable
    }
}
```
✔ This approach **avoids ambiguity** while still allowing reuse.

---

### **Key Takeaways**
1. **Java does not support multiple inheritance with classes** to avoid ambiguity.
2. **Multiple inheritance is allowed using interfaces** because they do not store instance variables.
3. **Use composition** instead of multiple inheritance if you need shared behavior.

Yes, in **method overriding**, the reference variable determines which methods can be accessed, but the **actual method execution depends on the object type** at runtime (dynamic method dispatch).

### **Key Points:**
1. **Reference type determines accessible members** → The reference variable's type dictates which methods and fields can be accessed at compile time.
2. **Object type determines overridden method execution** → If a method is overridden, the method in the **actual object's class** executes at runtime, not the method in the reference class.

### **Example:**
```java
class Parent {
    void show() {
        System.out.println("Parent's show()");
    }
}

class Child extends Parent {
    @Override
    void show() {
        System.out.println("Child's show()");
    }
}

public class Test {
    public static void main(String[] args) {
        Parent obj = new Child(); // Reference of Parent, Object of Child
        obj.show(); // Calls Child's show() due to dynamic method dispatch
    }
}
```

**Output:**
```
Child's show()
```
- Even though `obj` is of type `Parent`, the overridden `show()` method of `Child` is executed because **method overriding follows runtime polymorphism**.

### **But for variables:**
Instance variables **do not follow method overriding**. The reference type determines which variable is accessed.

```java
class Parent {
    int value = 10;
}

class Child extends Parent {
    int value = 20;
}

public class Test {
    public static void main(String[] args) {
        Parent obj = new Child();
        System.out.println(obj.value); // Prints 10 (Parent's value)
    }
}
```

**Key Takeaway:**
- **Methods follow runtime polymorphism (dynamic binding).**
- **Variables are resolved at compile time (static binding).**
Yes, **static methods do not follow method overriding** and are resolved at **compile-time** based on the reference type, not the object type. This is because static methods belong to the **class itself**, not the instance.

### **Key Differences:**
| Feature           | Instance Methods (Overridden) | Static Methods (Hidden) |
|------------------|---------------------------------|-------------------------|
| **Binding Time** | Runtime (Dynamic Binding)       | Compile-time (Static Binding) |
| **Accessed by**  | Object reference                | Class name or reference |
| **Execution**    | Based on the actual object type | Based on reference type |

---

### **Example: Static Methods Do Not Override**
```java
class Parent {
    static void display() {
        System.out.println("Parent's static display()");
    }
}

class Child extends Parent {
    static void display() {
        System.out.println("Child's static display()");
    }
}

public class Test {
    public static void main(String[] args) {
        Parent obj = new Child(); // Reference of Parent, Object of Child
        obj.display(); // Calls Parent's display()
    }
}
```

**Output:**
```
Parent's static display()
```

- Here, **`display()` is a static method**, so method resolution is done at **compile-time** based on the reference type (`Parent`), not the object type (`Child`).
- This is called **method hiding**, not overriding.

---

### **How Static Methods Differ from Instance Methods**
```java
class Parent {
    void instanceMethod() { System.out.println("Parent's instance method"); }
    static void staticMethod() { System.out.println("Parent's static method"); }
}

class Child extends Parent {
    @Override
    void instanceMethod() { System.out.println("Child's instance method"); }
    static void staticMethod() { System.out.println("Child's static method"); }
}

public class Test {
    public static void main(String[] args) {
        Parent obj = new Child();

        obj.instanceMethod(); // Runtime (Dynamic Binding) → Child's method executes
        obj.staticMethod();   // Compile-time (Static Binding) → Parent's method executes
    }
}
```

**Output:**
```
Child's instance method
Parent's static method
```

### **Key Takeaways:**
1. **Instance methods are overridden** and resolved at **runtime** based on the object type.
2. **Static methods are hidden** (not overridden) and resolved at **compile-time** based on the reference type.
3. **Access static methods via class name** to avoid confusion:
   ```java
   Parent.staticMethod(); // Calls Parent's method
   Child.staticMethod();  // Calls Child's method
   ```
In Java, **not everything** in an interface is `static` and `final`. However, there are strict rules for what is allowed inside an interface:

### 🚀 **Rules of Java Interfaces**
1. **Interface variables are `public`, `static`, and `final` by default.**  
2. **Methods in interfaces are `public` and `abstract` by default (before Java 8).**  
3. **From Java 8 onwards:**
   - You can have **default methods** (with implementation).
   - You can have **static methods** (with implementation).
4. **From Java 9 onwards:**  
   - You can have **private methods** inside interfaces.

---

### 🔥 **Why Are Interface Variables `static` and `final`?**
#### ✅ **1. Variables in Interfaces Are Meant to Be Constants**
- In an interface, all variables are automatically:
  ```java
  public static final
  ```
- This means:
  - `public` → Accessible everywhere.
  - `static` → Shared among all implementing classes.
  - `final` → Cannot be changed (constant).

🔹 Example:
```java
interface Config {
    int MAX_USERS = 100;  // Same as: public static final int MAX_USERS = 100;
}
```
Equivalent to:
```java
interface Config {
    public static final int MAX_USERS = 100;
}
```
✔ Since they are `static`, you can access them directly using the interface name:
```java
System.out.println(Config.MAX_USERS); // ✅ Works
```

#### ❌ Why Not Allow Non-Final Variables?
If interface variables were **not `final`**, every implementing class could **modify them**, breaking consistency.

---

### 🔥 **Why Are Methods in Interfaces Not `static` and `final`?**
1. **Methods are `abstract` by default** (before Java 8) → No implementation allowed.
2. **Methods cannot be `final` in an interface** because an interface is meant to be implemented (overridden).

---

### 🎯 **Evolution of Interface Methods**
#### ✅ **Before Java 8:**  
- Only `abstract` methods allowed.
```java
interface Animal {
    void makeSound(); // Implicitly public and abstract
}
```

#### ✅ **Java 8 Introduced `default` and `static` Methods:**  
```java
interface Animal {
    void makeSound(); // abstract

    default void eat() { // ✅ Can have implementation
        System.out.println("Eating...");
    }

    static void sleep() { // ✅ Static method with body
        System.out.println("Sleeping...");
    }
}
```

#### ✅ **Java 9 Introduced `private` Methods:**
```java
interface Animal {
    private void helper() { // ✅ Private method inside interface
        System.out.println("Helper method");
    }
}
```

---

### 🎯 **Conclusion**
- ✅ **Variables in interfaces are `static` and `final` because they act as constants.**
- ✅ **Methods are NOT `static` or `final` by default.** They are either:
  - `abstract` (default before Java 8),
  - `default` (with implementation from Java 8),
  - `static` (from Java 8),
  - `private` (from Java 9).
