# Lambdas and Functional Interfaces

* An interface with exactly one abstract method is a functional interface.

* Can (optionally) be annotated with @FunctionalInterface—the compiler will enforce the single-abstract-method rule.

```java
@FunctionalInterface
interface Converter<F,T> {
    T convert(F from);
    // default or static methods are allowed
    default void log(F from) { System.out.println(from); }
}
```

## Common Built-In Functional Interfaces (in java.util.function)

| Interface           | Abstract Method       | Signature              | Use Case                |
| ------------------- | --------------------- | ---------------------- | ----------------------- |
| `Supplier<T>`       | `T get()`             | no args → returns T    | lazy value producers    |
| `Consumer<T>`       | `void accept(T t)`    | consumes T             | side-effect operations  |
| `Function<T,R>`     | `R apply(T t)`        | maps T → R             | transformation          |
| `Predicate<T>`      | `boolean test(T t)`   | tests a condition on T | filters                 |
| `UnaryOperator<T>`  | `T apply(T t)`        | T → T                  | specialized Function    |
| `BinaryOperator<T>` | `T apply(T t1, T t2)` | (T,T) → T              | reduction               |
| `BiFunction<T,U,R>` | `R apply(T t, U u)`   | (T,U) → R              | two-arg transformations |



## Lambda Expression Syntax
### Basic Forms
```java
// No parameters
() -> System.out.println("Hello");

// Single parameter (type inferred, parentheses optional)
name -> name.toUpperCase();

// Multiple parameters (types optional, parentheses required)
(x, y) -> x + y;

// Block body (must use return)
(x, y) -> {
    int sum = x + y;
    return sum;
};
```

### Target Typing
* A lambda can only appear where the compiler knows which functional interface type it implements (e.g., a variable, parameter, or context expects a Predicate<String>).
```java
Predicate<String> p = s -> s.isEmpty();
List<String> list = List.of("a","", "b");
list.removeIf(s -> s.isBlank());  // removeIf expects Predicate<String>
```

### Method and Constructor References
Forms
* Static method: ClassName::staticMethod

* Instance method of particular object: instance::instanceMethod

* Instance method of an arbitrary object of a type: ClassName::instanceMethod

* Constructor: ClassName::new

```java
// Static
Function<Double, Double> sqrt = Math::sqrt;

// Particular instance
String prefix = "ID:";
Function<String, String> addId = prefix::concat;

// Arbitrary instance
BiPredicate<String,String> eq = String::equalsIgnoreCase;

// Constructor
Supplier<ArrayList<String>> listSupplier = ArrayList::new;
```

### Variable Capture & “Effectively Final”
* Lambdas can reference local variables, but those must be final or effectively final (never re-assigned after initialization).

* Instance fields and static fields may be read or modified freely.

```java
int factor = 2;    // effectively final
Function<Integer,Integer> multiply = x -> x * factor;
// factor = 3;    // compile error if you uncomment (factor not effectively final)
```

### Using Lambdas with Collections
forEach
```java
List<String> names = new ArrayList<>(List.of("Alice","Bob","Carol"));
names.forEach(name -> System.out.println(name.toUpperCase()));
```

removeIf
```java
names.removeIf(s -> s.length() < 4);  // removes "Bob"
```

replaceAll
```java
names.replaceAll(String::toLowerCase);
```

sort
```java
List<Person> people = …;
people.sort(Comparator.comparing(Person::getLastName)
                      .thenComparing(Person::getFirstName));
```

## Common Pitfalls & Exam Tips
Ambiguous overloads
```java
void foo(Function<String,String> f) {}
void foo(UnaryOperator<String> f) {}
// foo(s -> s.trim()); // compile error: ambiguous, both match
```

Wrong context
```java
Runnable r = () -> { return 5; };
// Compile error: Runnable.run() returns void
```

this inside lambda

* Refers to enclosing instance, not to a hidden lambda object.

```java
class A {
  Runnable r = () -> System.out.println(this.getClass().getName());
}
```

Serializable lambdas
* Not serializable by default—do not expect to cast to Serializable.

Exceptions in lambdas
* Checked exceptions must be declared by the functional interface. E.g., Consumer<T> can’t throw checked exceptions.

## List & ArrayList Refresher (for context)
```java
List<String> list = new ArrayList<>();  // declare and instantiate
list.add("x");
list.add("y");
list.forEach(System.out::println);
```

* Use the List interface type for programming to an interface.

* ArrayList provides a resizable-array implementation.

