# Building Blocks

### Objectives covered in this chapter

**Working With Java Primitive Data Types and String APIs**
* Declare and initialize variables (including casting and promoting
primitive data types)

* Identify the scope of variables

* Use local variable type inference

**Describing and Using Objects and Classes**

* Declare and instantiate Java objects, and explain objects’ lifecycles
(including creation, dereferencing by reassignment, and garbage
collection)

* Read or write to object fields

## Creating objects 
**Object** is an instance of a class.

**CALLING CONSTRUCTORS** 
```java
Park p = new Park();
```
This gives Java a place to store a reference to the
object.
Then you write new Park() to actually create the object.

There are two key points to note about the constructor: 
* the name of
the constructor matches the name of the class, and 
* there’s no return
type.

* The purpose of a constructor is to initialize fields

* You can’t refer to a
variable before it has been defined

### What do you think this code prints out?

```java
public class Egg {
public Egg() {
number = 5;
}
public static void main(String[] args) {
Egg egg = new Egg();
System.out.println(egg.number);
}
private int number = 3;
{ number = 4; } }
```

**If you answered 5, you got it right. Fields and blocks are run first in
order, setting number to 3 and then 4. Then the constructor runs,
setting number to 5.**


## Understanding Data Types

Java applications contain two types of data: 
* primitive types
* reference types


| Keyword | Type | Example |
|----------|----------|----------|
| boolean | true or false   | true   |
| byte      | 8-bit integral value   | 123   |
| short    | 16-bit integral value  |123   |
| char | 16-bit Unicode value   | 'a'  |
| int| 32-bit integral value  | 123  |
| long     | 64-bit integral value  | 123L   |
| double  | 64-bit floating-point value  |123.456  |

### CHAR && SHORT
* The primary difference is that short is
signed, which means it splits its range across the positive and
negative integers. Alternatively, char is unsigned, which means
range is strictly positive including 0. Therefore, char can hold a
higher positive numeric value than short, but cannot hold any
negative numbers.
```java
short bird = 'd';
char mammal = (short)83;

System.out.println(bird); // Prints 100
System.out.println(mammal); // Prints S

// Restrictions 
short reptile = 65535; // DOES NOT COMPILE
char fish = (short)-1; // DOES NOT COMPILE
```

### Literals and the Underscore Character

```java
int million1 = 1000000; ✅
int million2 = 1_000_000;✅

others examples
double notAtStart = _1000.00; ❌
double notAtEnd = 1000.00_; ❌
double notByDecimal = 1000_.00; ❌
double annoyingButLegal = 1_00_0.0_0; ✅
double reallyUgly = 1__________2; ✅

```

### IDENTIFYING IDENTIFIERS
There are only four rules to remember for legal identifiers:
* Identifiers must begin with a letter, a $ symbol, or a _ symbol.
* Identifiers can include numbers but not start with them.
* Since Java 9, a single underscore _ is not allowed as an identifier.
* You cannot use the same name as a Java reserved word. 


```java
legal: 
long okidentifier;
float $OK2Identifier;
boolean _alsoOK1d3ntifi3r;
char __SStillOkbutKnotsonice$;

Ilegal:
int 3DPointClass; // identifiers cannot begin with a number
byte hollywood@vine; // @ is not a letter, digit, $ or _
String *$coffee; // * is not a letter, digit, $ or _
double public; // public is a reserved word
short _; // a single underscore is not allowed


int num, String value; // DOES NOT COMPILE declare multiple variables of different types in the same statement.
```
### INTRODUCING VAR
Local variable type inference works with local variables and not instance variables.

```java
public void whatTypeAmI() {
var name = "Hello";
var size = 7;
}

public class VarKeyword {
var tricky = "Hello"; // DOES NOT COMPILE
}


7: public void reassignment() {
8: var number = 7;
9: number = 4;
10: number = "five"; // DOES NOT COMPILE
11: }
```

1. A var is used as a local variable in a constructor, method, or
initializer block.
2. A var cannot be used in constructor parameters, method parameters,
instance variables, or class variables.
3. A var is always initialized on the same line (or statement) where it is
declared.
4. The value of a var can change, but the type cannot.

5. A var cannot be initialized with a null value without a type.
6. A var is not permitted in a multiple-variable declaration.
7. A var is a reserved type name but not a reserved word, meaning it
can be used as an identifier except as a class, interface, or enum name.

### Scope

* Local variables: In scope from declaration to end of block
* Instance variables: In scope from declaration until object eligible for
garbage collection
* Class variables: In scope from declaration until program ends

### GARBAGE COLLECTION
Garbage collection refers to the process of automatically freeing
memory on the heap by deleting objects that are no longer reachable
in your program.

How does the JVM know when an object is eligible for garbage
collection?

1. The object no longer has any references pointing to it.
2. All references to the object have gone out of scope.
















