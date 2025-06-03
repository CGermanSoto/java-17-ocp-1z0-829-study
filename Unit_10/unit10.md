# Modules

## What Is the Modular JDK?
* Starting in Java 9, the JDK itself is divided into modules. You no longer have a monolithic rt.jar.

* Core modules include:
    * java.base – always present (contains java.lang, java.util, etc.).

    * java.logging, java.sql, java.xml, java.desktop, java.compiler, and many more.

You can list all modules in the JDK with:
```java
$ java --list-modules
java.base@17.0.1
java.compiler@17.0.1
java.datatransfer@17.0.1
java.desktop@17.0.1
java.logging@17.0.1
java.sql@17.0.1
...
```
* java.base is always available and does not need to be explicitly required. Every module automatically reads java.base.

## Why a modular JDK?
* Smaller runtime images: you can assemble a custom runtime with only the modules you need using jlink.

* Stronger encapsulation: modules only expose what you explicitly declare via exports.

* Better maintainability: modules specify their dependencies via requires, making the JDK’s internal dependencies explicit and discoverable.

## Declaring Your Own Module

Every module you create must contain a module-info.java file at its root. This file instructs the compiler and runtime about:

1. Module name.

2. Which other modules it requires.

3. Which of its own packages it exports.

4. (Optionally) Services it uses or provides.

## Minimal module-info.java Structure

```java
module com.myapp.moduleName {
    requires java.logging;        // depends on the java.logging module
    exports com.myapp.api;        // makes com.myapp.api visible to other modules
    // (optional) opens com.myapp.internal to some.module;  // reflection-access
    // (optional) uses com.myapp.spi.ServiceInterface;
    // (optional) provides com.myapp.spi.ServiceInterface with com.myapp.spi.ServiceImpl;
}
```

* module com.myapp.moduleName { … }: must match the directory structure under src. For example, if your module name is com.myapp.moduleName, the source root might be:
```java
src/
  com.myapp.moduleName/
    module-info.java
    com/
      myapp/
        api/
          MyInterface.java
        impl/
          MyImplementation.java
```

* requires X;: declares a dependency on module X.
    * If X is not found on the module path at compile or run time, you get a compile- or runtime error.

    * You cannot requires a module that doesn’t exist.

* exports com.myapp.api;: allows other modules to see public types in com.myapp.api.
    * If you don’t export a package, even if its classes are public, they remain invisible to other modules.

    * Within the module, you can always access your own packages regardless of exports.

## Automatic Modules & the Unnamed Module
* If you place a JAR on the module path that does not contain a module-info.class, Java treats it as an anonymous “automatic module”. Its name is derived from the JAR filename (e.g., my-library-1.2.jar → module name my.library).

* Any code on the classpath (legacy approach) is considered to be in the “unnamed module.”
    * The unnamed module can read (i.e., depend on) all modules, but no module can read the unnamed module.

## requires Variants
### requires vs requires transitive
#### requires modX;
* Your module directly depends on modX.

* Consumers of your module do not automatically see modX. They must explicitly requires modX if they need to use it.

#### requires transitive modX;
* Your module depends on modX and re-exports it to any module that requires your module.

* Example:
```java
module com.library.loggingFacade {
    requires transitive java.logging;
    exports com.library.logger;
}
```

* Now if com.app.main does requires com.library.loggingFacade;, it can also see java.logging without having to declare requires java.logging; itself.

### requires static

* Declares a compile-time dependency but does not require the module at runtime.

* Useful for tools or libraries only needed during compilation (e.g., annotation processors).

```java
module com.myapp {
    requires static com.squareup.javapoet; // only needed to generate code at compile time
    requires java.base;
}
```

* At runtime, if com.squareup.javapoet is absent, no runtime error occurs (unlike a plain requires).

### exports and Strong Encapsulation
* exports <package-name>; makes a package visible to all modules on the module path.

* If you want to grant access only to specific modules, use:
```java
exports com.myapp.internal to com.trusted.module, com.other.module;
```

* Now only com.trusted.module and com.other.module can read com.myapp.internal. No one else can.

* Any package not exported is completely hidden from other modules.
    * Example
    ```java
    module com.myapp.core {
    exports com.myapp.api;            // public API
    exports com.myapp.utils to com.myapp.tools;   // internal API only to tools module
    // com.myapp.impl is not exported → other modules cannot see it
    }
    ```

* Within com.myapp.core itself, everything is visible; encapsulation only applies across module boundaries.

### opens and Reflection
* exports is for compile-time and runtime reflective access to public types.

* opens is for runtime-only reflective access, particularly for frameworks (e.g., JSON serializers, dependency injection) that use reflection to access private or package-private members.

```java
module com.myapp {
    opens com.myapp.models to com.fasterxml.jackson.databind; 
}
```

* Now Jackson can use reflection to read/write fields in com.myapp.models, even private ones.

* If you just did exports com.myapp.models;, only public types and public members are accessible; private fields remain inaccessible to reflective frameworks.

* You can also do a qualified open:
```java
opens com.myapp.secret.impl to com.myapp.admin; 
```

* Only com.myapp.admin can use reflection on the members of com.myapp.secret.impl.

## Service Providers: uses & provides

Modules enable a service‐loader mechanism at the module level.

### Service Interface (uses)
If your module needs an implementation of a service interface:
```java
module com.myapp.consumer {
    requires com.myapp.api; 
    uses com.myapp.api.PaymentProcessor;  
}
```

* This tells the module system “At runtime, I expect someone to provide a PaymentProcessor implementation.”

##  Service Implementation (provides … with …)

In the module that provides the implementation:

```java
module com.myapp.stripe {
    requires com.myapp.api;
    provides com.myapp.api.PaymentProcessor 
             with com.myapp.stripe.StripePaymentProcessor;
}
```
* At runtime, when ServiceLoader.load(PaymentProcessor.class) is called from the consumer, the system will find StripePaymentProcessor and instantiate it.

* Multiple providers can be listed, separated by commas.

## Putting It All Together: A Sample Multi-Module Project
Imagine three modules:
1. com.greetings.api – defines an interface.

2. com.greetings.provider – implements the interface as a service.

3. com.app.main – consumes the service.

### Directory Structure
```java
project-root/
└─ src/
   ├─ com.greetings.api/
   │   ├─ module-info.java
   │   └─ com/
   │       greetings/
   │           GreetingService.java
   │
   ├─ com.greetings.provider/
   │   ├─ module-info.java
   │   └─ com/
   │       greetings/
   │           impl/
   │               EnglishGreetingService.java
   │
   └─ com.app.main/
       ├─ module-info.java
       └─ com/
           app/
               Main.java
```

### com.greetings.api/module-info.java

```java
module com.greetings.api {
    // This module defines the service interface
    exports com.greetings;   // packages with public API
}
```

com/greetings/GreetingService.java
```java
package com.greetings;

public interface GreetingService {
    String greet(String name);
}
```

com.greetings.provider/module-info.java
```java
module com.greetings.provider {
    requires com.greetings.api;                 // depends on the API module
    provides com.greetings.GreetingService 
             with com.greetings.impl.EnglishGreetingService;
}
```

com/greetings/impl/EnglishGreetingService.java
```java
package com.greetings.impl;

import com.greetings.GreetingService;

public class EnglishGreetingService implements GreetingService {
    @Override
    public String greet(String name) {
        return "Hello, " + name + "!";
    }
}
```

com.app.main/module-info.java
```
module com.app.main {
    requires com.greetings.api;  // needs the service interface
    uses com.greetings.GreetingService;  // will use a service provider
}
```

com/app/Main.java
```java
package com.app;

import com.greetings.GreetingService;
import java.util.ServiceLoader;

public class Main {
    public static void main(String[] args) {
        ServiceLoader<GreetingService> loader 
            = ServiceLoader.load(GreetingService.class);

        for (GreetingService service : loader) {
            System.out.println(service.greet("James"));
        }
    }
}
```

## Compiling a Multi-Module Project
Assume you are in project-root:
* Compile modules individually using javac with --module-source-path:
```java
javac -d out \
      --module-source-path src \
      $(find src -maxdepth 2 -type f -name "*.java")
```

* --module-source-path src tells the compiler to treat each subdirectory under src as a separate module named by that subdirectory.

* The result goes into out/com.greetings.api, out/com.greetings.provider, and out/com.app.main directories, each containing compiled classes and module-info.class.

Verify module graph with:
```java
java --list-modules --module-path out
```

You should see your three modules listed, plus any JDK modules they depend on.

## Running a Modular Application

Use the java command with --module-path and --module:

```java
java --module-path out \
     --module com.app.main/com.app.Main
```

* --module-path out: tells the JVM where to find modules.

* --module com.app.main/com.app.Main: instructs “run the Main class from module com.app.main.”

    * The JVM will automatically resolve and load com.greetings.api and com.greetings.provider because:
        1. com.app.main uses com.greetings.GreetingService.
        2. com.greetings.provider provides that service.
    * If any required module is missing, you get a runtime resolution error.


## Compiling a Single Module (Quick Example)

### Simple Module “Hello”
```java
hello-mod/
├─ src/
│  ├─ module-info.java
│  └─ com/
│      └─ example/
│          └─ Hello.java
```

module-info.java
```java
module com.example.hello {
    exports com.example;
}
```

Hello.java
```java
package com.example;

public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, Modules!");
    }
}
```

Compilation
```java
$ javac -d mods/com.example.hello src/com.example.hello/module-info.java \
      src/com.example.hello/com/example/Hello.java
```

* -d mods/com.example.hello places compiled classes under mods/com.example.hello.

* Now you have mods/com.example.hello/module-info.class and mods/com.example.hello/com/example/Hello.class.

Running
```java
$ java --module-path mods --module com.example.hello/com.example.Hello
Hello, Modules!
```

### Unnamed Module & Classpath Interactions
* Unnamed Module: all classes on the traditional classpath belong here.

* Unnamed module can read any named module on the module path, but named modules cannot read the unnamed module (no “reverse access”).

* If you place a JAR on both classpath and module path, the module path version wins; the classpath version is ignored.

### Splitting Packages and Module Boundaries
* You cannot “split” a single package across multiple modules; i.e., two different modules cannot declare classes in com.example.shared.

* Attempting to compile a project where moduleA and moduleB both have com.example.shared.SomeClass results in a compiler error: “Package com.example.shared appears in both moduleA and moduleB.”

* Always keep packages unique to a single module.

### Automatic Modules vs Explicit Modules
* If you put my-lib.jar on the module path, the system creates an automatic module named my.lib (dots replace hyphens).

* Automatic modules:
    * Export all packages in the JAR to every other module.

    * Require all other modules on the module path automatically (i.e., you can call any JDK module without adding a requires).
* However, automatic modules are a transitional feature; you should prefer writing proper module-info.java when you maintain your own libraries.

## Modular Compilation Flags (Advanced)
* --module-path <dirs> (or -p): where to find compiled modules.

* --add-modules: forces the inclusion of a module (useful if you are using reflection to load something).

* --add-reads A=B: makes module A read module B even if it did not declare it.

* --add-exports A/pkg=B: opens A’s package pkg to B at runtime (reflection).

* --patch-module A=someDir: patches classes into module A from a directory (mostly for testing).

These flags are important if you are migrating a large, legacy codebase or doing advanced testing, but most exam questions focus on writing module-info.java and the basic javac/java commands.

