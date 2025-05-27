# Welcome to Java 🧑🏻‍💻

### Objectives covered in this chapter

**Understanding Java Technology and Environment**
* Describe Java Technology and the Java development environment
* Identify key features of the Java language

**Creating a Simple Java Program**
* Create an executable Java program with a main class
* Compile and run a Java program from the command line
* Create and import packages

**Describing and Using Objects and Classes**
* Define the structure of a Java class


**DK (Java Development Kit)
Includes the essentials for Java development:**

1. javac: compiles .java to .class (bytecode)

2. java: runs bytecode on the JVM

3. JVM: interprets bytecode on your machine



## Understanding the Java Class Structure: Members of the class

### Fields and Methods
* Fields = hold the state of the program
* Methods = operate on that state

### Comments
```java
// Comment until end of line

/*
 * Comentario de tres líneas
 */

/**
* Javadoc multiple-line comment
* @author Germán Soto
*/
```

## Classes vs. Files

### About Classes

* You can even put two classes in the same file. at most one of the classes in the file is allowed to
be public. 
* Each file can contain only one public class.
* The filename must match the class name, including case, and have a .java extension.

```java
1: public class Animal {
2: private String name;
3: }
4: class Animal2 {
5: }
```
### Creating a main() Method

The main() method lets the JVM call our code. The simplest possible class with a main() method looks like this:
```java
1: public class Zoo {
2:      public static void main(String[] args) {
3:          //
4:      }
5: }
```

### Exam Essentials
**Identify benefits of Java.** 
Benefits of Java include object-oriented design, encapsulation, platform independence,
robustness, simplicity, security, multithreading, and backward compatibility.

**Define common acronyms.**
The JDK stands for Java Development Kit and contains the compiler and JVM
launcher. The JVM stands for Java Virtual Machine, and it runs bytecode. API is an application programming
interface, which is code that you can call.

**Be able to write code using a main() method.**
A main() method is usually written as public static void
main(String[] args). Arguments are referenced starting with args[0]. Accessing an argument that wasn’t passed in
will cause the code to throw an exception.

**Understand the effect of using packages and imports.**
Packages contain Java classes. Classes can be imported
by class name or wildcard. Wildcards do not look at subdirectories. In the event of a conflict, class name imports
take precedence.

**Be able to recognize misplaced statements in a class.**
Package and import statements are optional. If present,
both go before the class declaration in that order. Fields and methods are also optional and are allowed in any
order within the class declaration.

















