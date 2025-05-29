# Class Design

## Inheritance Basics

### Declaring Subclasses
```java
class Animal { 
    String name; 
    Animal(String name) { this.name = name; } 
}

class Cat extends Animal {
    int lives;
    Cat(String name, int lives) {
        super(name);         // must be first statement
        this.lives = lives;
    }
}
```

* extends: single inheritance only.

* super(...): invokes parent constructor; if omitted, compiler inserts super() (no-arg).

### final Classes and Methods
* final class cannot be subclassed.

* final method cannot be overridden.

```java
final class Immutable {}

// In subclass:
class A {
  public final void foo() {}
}
class B extends A {
  // public void foo() {} // ❌ compile error
}
```

## Method Overriding and Polymorphism
### Overriding Rules
* Same signature (name + parameter types).

* Return type must be same or covariant subtype.

* Cannot reduce visibility (e.g. protected → private).

* Cannot throw new checked exceptions not declared in superclass.

```java
class Parent {
    protected Number getValue() throws IOException { … }
}
class Child extends Parent {
    @Override
    public Integer getValue() throws FileNotFoundException { … }
}
```

## Dynamic Dispatch
* Runtime decides which override to call, based on actual object, not reference type:

```java
Parent p = new Child();
p.getValue();   // invokes Child.getValue()
```

## Overloading vs Overriding vs Hiding
| Concept           | Signature                    | When Resolved | Return Type Rules                  |
| ----------------- | ---------------------------- | ------------- | ---------------------------------- |
| **Overload**      | same name, different params  | Compile-time  | Any return; not part of resolution |
| **Override**      | same name & params           | Runtime       | Same or covariant                  |
| **Hide (static)** | static method same signature | Compile-time  | Must have same return type         |

```java
class A {
    static void stat() {}
    void inst() {}
}

class B extends A {
    static void stat() { System.out.println("hidden"); }
    @Override
    void inst() { System.out.println("overridden"); }
}

A a = new B();
a.stat();  // calls A.stat()  (hidden, compile-time)
a.inst();  // calls B.inst()  (overridden, runtime)
```

## Abstract Classes & Methods
* abstract class may have abstract methods and cannot be instantiated.

* abstract method has no body; subclass must override or remain abstract.

```java
abstract class Shape {
    abstract double area();
    void print() { System.out.println(area()); }
}

class Circle extends Shape {
    double radius;
    Circle(double r) { radius = r; }
    @Override
    double area() { return Math.PI * radius * radius; }
}
```

### Casting and instanceof
* Upcast (Safe)

```java
Cat c = new Cat("Whiskers", 9);
Animal a = c;    // implicit upcast
```

### Downcast (Requires explicit cast + runtime check)
```java
Animal a = new Cat("Milo", 7);
if (a instanceof Cat) {
    Cat c2 = (Cat)a;   // safe downcast
}
```

* ClassCastException if invalid at runtime.

### Constructors & Initialization Order
* Static fields & static init blocks (in textual order).

* Instance fields & instance init blocks (in textual order).

* Constructor body.

```java
class InitOrder {
    static { System.out.println("Static init"); }
    { System.out.println("Instance init"); }
    InitOrder() { System.out.println("Constructor"); }
    public static void main(String[] args) {
        new InitOrder();
    }
}
// Output:
// Static init
// Instance init
// Constructor
```

### Field and Method Access in Inheritance

* private fields/methods are invisible to subclasses.

* protected members visible in same package and subclasses.

* public everywhere.

* Package-private (no modifier) visible only within same package.

### Polymorphic Collections
* You can store subclass instances in collections of superclass type:
```java
List<Animal> zoo = new ArrayList<>();
zoo.add(new Cat("Felix", 9));
zoo.add(new Dog("Rex"));
for (Animal a : zoo) {
    System.out.println(a.getClass().getSimpleName() + ": " + a.name);
}
```
* Method calls are dynamically dispatched.

> Pitfalls
* Calling non-static super methods
```java
class A { void m() {} }
class B extends A {
  static void m() {}  // hides, not overrides
}
``` 
* Covariant return pitfalls
```java
class P { Number get() {…} }
class C extends P { Integer get() {…} }
P p = new C();
Number n = p.get();  // OK
```

* this() / super()
    * Must be first line in constructor.

    * Only one of them per constructor.

* Abstract instantiation
```java
abstract class X {} 
// new X(); // ❌ cannot instantiate abstract
```

* Hidden static vs overridden instance
```java
A a = new B();
a.staticMethod();   // calls A’s version
a.instanceMethod(); // calls B’s override
```

* Downcasting without check → runtime failure:
```java
Animal a = new Dog();
Cat c = (Cat)a;     // ClassCastException
```



