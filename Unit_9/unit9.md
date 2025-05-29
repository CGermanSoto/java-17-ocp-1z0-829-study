# Exceptions

## The Exception Hierarchy

```java
java.lang.Throwable
  ├─ java.lang.Error            // Serious system errors — do not catch
  └─ java.lang.Exception
       ├─ java.lang.RuntimeException  // Unchecked exceptions
       │    ├─ NullPointerException
       │    ├─ ArrayIndexOutOfBoundsException
       │    └─ IllegalArgumentException
       └─ (other checked exceptions)
            ├─ IOException
            ├─ SQLException
            └─ ReflectiveOperationException
```

* Error
> Represents serious JVM problems (e.g., OutOfMemoryError). Do not catch or declare them.

* Checked exceptions
> Subclasses of Exception excluding RuntimeException. Must be either caught or declared in the method signature.

* Unchecked exceptions
> RuntimeException and its subclasses. Do not need to be declared or caught. Represent programming bugs or unchecked failures.

## Throwing Exceptions
* throw Statement
Explicitly create and raise an exception object:
```java
public void connect(String url) {
    if (url == null) {
        throw new IllegalArgumentException("URL cannot be null");
    }
    // ...
}
```

You can throw any Throwable, but best practice is to throw subclasses of Exception (checked or unchecked), rarely Error.

## Declaring with throws
* If your method might throw a checked exception you do not handle, you must declare it:
```java
public String readFile(String path) throws IOException {
    FileReader fr = new FileReader(path);   // new FileNotFoundException(path) is thrown
    // ...
    return content;
}
```

* A method can declare multiple exceptions:
```java
void risky() throws IOException, SQLException { … }
```
* throws is part of the method signature—it does not itself handle anything.

## Handling Exceptions: try / catch / finally
* Basic Form
```java
try {
    // code that might throw
    int result = 10 / divisor;
    System.out.println("Result = " + result);
} catch (ArithmeticException ae) {
    System.err.println("Division by zero!");
} finally {
    System.out.println("Cleanup actions always run.");
}
```

* try block: wraps code that may throw.

* catch block(s): one or more, each specifying a parameter type.

* finally block: always executes after try/catch, even if you return inside them (except in extreme JVM crashes). Use for cleanup (closing streams, releasing locks).

## Multiple Catches & Ordering
```java
try {
    methodThatThrows();
} catch (FileNotFoundException fnf) {
    // specific handler
} catch (IOException ioe) {
    // more general
} catch (Exception e) {
    // catch-all
}
```

* Order matters: place most specific first.

* A catch parameter type must not be a subtype of any earlier catch (compiler error).

## Multi-catch (Java 7+)
```java
try {
    // ...
} catch (IOException | SQLException e) {
    // handles both
}
```

* In a multi-catch, the exception variable e is effectively final—you cannot reassign it inside the block.

## Try-with-Resources (Java 7+)
* Automatic resource management for any AutoCloseable:
```java
public void copy(File src, File dst) throws IOException {
    try (InputStream in = new FileInputStream(src);
         OutputStream out = new FileOutputStream(dst)) {
        byte[] buf = new byte[8192];
        int len;
        while ((len = in.read(buf)) != -1) {
            out.write(buf, 0, len);
        }
    } // in.close() and out.close() are called automatically
}
```

* Resources declared in parentheses are closed in reverse order of creation.

* If both the try block and close() throw, the one from try is propagated and the closeable’s exception is suppressed (accessible via Throwable.getSuppressed()).

## How Exceptions Alter Control Flow
* When an exception is thrown, execution jumps immediately out of the try block to the matching catch.

* Any code between the throw point and the catch is skipped, including later statements in the try.

* After the catch, the finally executes.

* If no catch matches, the exception propagates to the caller.

```java
try {
    System.out.println("A");
    if (true) throw new RuntimeException("Boom");
    System.out.println("B"); // never reached
} catch (RuntimeException e) {
    System.out.println("C");
} finally {
    System.out.println("D");
}
System.out.println("E");
```

Output:
```java
A
C
D
E
```

## Checked vs Unchecked in Practice
| Scenario             | Exception Type                       | Must Declare/Catch? |
| -------------------- | ------------------------------------ | ------------------- |
| Missing file on disk | `FileNotFoundException` (checked)    | Yes                 |
| Bad input from user  | `IllegalArgumentException`           | No                  |
| Divide by zero       | `ArithmeticException`                | No                  |
| Out of memory (JVM)  | `OutOfMemoryError`                   | No (shouldn’t)      |
| JSON parsing error   | typically a checked `ParseException` | Yes                 |

## Creating Custom Exceptions
* Checked Exception
```java
public class InsufficientFundsException extends Exception {
    public InsufficientFundsException(String msg) {
        super(msg);
    }
}

// Usage:
public void withdraw(double amt) throws InsufficientFundsException {
    if (balance < amt) throw new InsufficientFundsException("Not enough funds");
    balance -= amt;
}
```

## Unchecked Exception
```java
public class InvalidConfigurationException extends RuntimeException {
    public InvalidConfigurationException(String msg) {
        super(msg);
    }
}
```

* Choose checked when the caller is expected to recover or explicitly handle the condition.

* Choose unchecked for programming errors or unrecoverable conditions.

# Best Practices & Exam Pitfalls
* Never catch Error—these indicate serious system failures.

* Catch the narrowest exception you can handle.

* Don’t swallow exceptions (empty catch blocks) without logging or re-throwing.

* finally vs return: a return in finally will override any prior return or exception—avoid this.

```java
int m() {
  try { return 1; }
  finally { return 2; } // always returns 2
}
```
* Multi-catch cannot catch subtypes in the same clause (no IOException | FileNotFoundException).

* Declaring throws RuntimeException is legal but pointless—unchecked exceptions need no declaration.



