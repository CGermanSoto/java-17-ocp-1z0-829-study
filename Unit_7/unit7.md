# Methods and Encapsulation

* Modifiers include public, private, protected, static, final, abstract, etc.
* ReturnType may be any type or void.
* Parameters must be declared as <type> <name>, separated by commas.

### Method Overloading
* Same method name, different parameter list (number/types/order).

* Return type can differ, but it doesn’t help differentiate overloads.

```java
void walk(int miles) {}
void walk(int miles, double speed) {}
// void walk(double speed, int miles) {} → also valid
// int walk(int miles) {} → invalid if above exists: same params!
```

### Varargs
* Used for variable-length arguments.

* Only one vararg per method, must be the last parameter.

```java
public void printNames(String... names) {
    for (String name : names) System.out.println(name);
}

// Valid calls
printNames(); 
printNames("Alice", "Bob");
```

### Constructors
* Same name as class, no return type.

* Can be overloaded.

* If no constructor is defined, Java provides a default no-arg constructor.

```java
public class Dog {
    public Dog() {}                    // no-arg constructor
    public Dog(String name) {}         // overloaded
}
```
* Can call another constructor using this() (must be first statement).

* Can call superclass constructor using super() (also must be first statement).

### Access Modifiers

| Modifier    | Same Class | Same Package | Subclass (diff pkg) | Everywhere |
| ----------- | ---------- | ------------ | ------------------- | ---------- |
| `private`   | ✅          | ❌            | ❌                   | ❌          |
| default     | ✅          | ✅            | ❌                   | ❌          |
| `protected` | ✅          | ✅            | ✅                   | ❌          |
| `public`    | ✅          | ✅            | ✅                   | ✅          |

```java
package zoo;
public class Animal {
    protected String name;
}

package zoo.birds;
import zoo.Animal;
public class Parrot extends Animal {
    void print() {
        System.out.println(name); // accessible because Parrot is subclass
    }
}
```

### Encapsulation

* Encapsulation = hiding data via private fields + providing public accessors (getters/setters).

```java
public class Person {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        if (!name.isBlank()) this.name = name;
    }
}
```

* Fields should be private.

* Public getter/setter methods should provide controlled access.

* Common in POJOs and JavaBeans.

### Static Methods and Fields

* Belong to the class, not instances.

* Can be accessed using ClassName.method() or from other static methods.

```java
public class MathUtils {
    public static int square(int x) {
        return x * x;
    }

    public static void main(String[] args) {
        System.out.println(MathUtils.square(5));
    }
}
```
* Static methods cannot access instance fields/methods directly.

* Instance methods can access static members.

### Final Methods
* A final method cannot be overridden in a subclass.
```java
public class Base {
    public final void show() {}
}

public class Sub extends Base {
    // public void show() {} // ❌ compile error
}
```

### Return Types and void
* Methods can return a value or void (no return).

* For non-void methods, return must return the correct type or a compatible value.

* You can return early with return; in void methods.

```java
public String greet(String name) {
    if (name == null) return "Hello, stranger!";
    return "Hello, " + name;
}
```

### Initialization Blocks
* Instance Initializer Block runs when object is created (after fields, before constructor).

* Static Initializer Block runs once when class is first loaded.

```java
public class InitTest {
    static { System.out.println("Static init"); }
    { System.out.println("Instance init"); }

    public InitTest() {
        System.out.println("Constructor");
    }

    public static void main(String[] args) {
        new InitTest();
        new InitTest();
    }
}
// Output:
// Static init
// Instance init
// Constructor
// Instance init
// Constructor
```

### Exan Pitfalls

> Pitfalls
>❌ Overloading by return type alone is not allowed.
>
>❌ Varargs must come last in parameter list.
>
>❌ Calling this() or super() must be first statement in constructor.
>
>❌ Static methods cannot access instance variables.
>
>✅ Private methods can be overloaded, but not overridden.
>
>✅ Constructors don’t have return types.
>
>❌ You cannot call a constructor like a method (this.method() ≠ this()).
>
>✅ A constructor can call another constructor from the same class using this().