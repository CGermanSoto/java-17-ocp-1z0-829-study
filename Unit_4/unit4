# Making Decisions

## if/ else

Syntax
```Java
if (condition) {
    // execute when condition is true
} else {
    // execute when condition is false
}
```
Rules & Tips
* The condition must be a boolean expression.
* Curly braces {} are optional for single statements, but always use them to avoid mistakes.

```java
int x = 5;
if (x > 0) {
    System.out.println("Positive");
} else {
    System.out.println("Non-positive");
}

// Omitting braces (legal but error-prone):
if (x > 0)
    System.out.println("Yes");
    System.out.println("Always runs"); // not part of the if!
```

## Switch

Syntax
```java
switch (variable) {
    case LITERAL_1 -> expressionOrBlock;
    case LITERAL_2, LITERAL_3 -> {
        // multiple labels, block form
    }
    default -> fallbackExpression;
}
```

Key Points
* Switch variable can be byte, short, char, int, String, or an enum.
* Case labels must be compile-time constants.
* break is not needed with -> form (no fall-through).
* You can still use the classic case : … break; form if you need fall-through.

```java
String day = "MON";
switch (day) {
    case "MON" -> System.out.println("Monday");
    case "TUE", "WED" -> System.out.println("Midweek");
    default -> System.out.println("Other day");
}
```

## While Loop

Syntax
```java
int i = 0;
while (i < 3) {
    System.out.println(i);
    i++;
}
// Prints 0, 1, 2
```
Rules
* Evaluate condition before entering the loop.
* If condition is false initially, body never executes.

## do-while Loop
Syntax
```java
int i = 0;
do {
    System.out.println(i);
    i++;
} while (i < 0);
// Prints 0 exactly once
```
Rules
* Body executes at least once, then evaluates condition.

## for Loop
Syntax
```java
for (int i = 0; i < 3; i++) {
    System.out.println(i);
}
// Prints 0, 1, 2
```

Components
* Initialization: executed once before loop

* Condition: boolean check before each iteration

* Update: executed after each iteration

## Enhanced for-each Loop
Syntax
```java
String[] fruits = {"apple", "banana", "cherry"};
for (String f : fruits) {
    System.out.println(f);
}
```

Rules
* Works with arrays or any Iterable<T>.

* You cannot modify the collection’s structure (e.g., remove elements) during iteration.

## Nested Loops
Syntax
```java
for (int row = 1; row <= 3; row++) {
    for (int col = 1; col <= 2; col++) {
        System.out.println("Cell " + row + "," + col);
    }
}
```


## break and continue

break
* Exits the nearest enclosing loop or switch.

* In nested loops, only breaks out of the innermost one unless labeled.

```java
for (int i = 0; i < 5; i++) {
    if (i == 3) break;
    System.out.println(i);
}
// Prints 0, 1, 2

//Labeled break
outer:
for (int i = 1; i <= 3; i++) {
    for (int j = 1; j <= 3; j++) {
        if (i * j > 4) break outer;
        System.out.println(i + "*" + j);
    }
}
// Stops both loops once i*j > 4
```

continue 
* Skips the rest of the loop body and proceeds with the next iteration.
* For for loops, executes the update step before re-evaluating the condition.

```java
for (int i = 0; i < 5; i++) {
    if (i % 2 == 0) continue; 
    System.out.println(i);
}
// Prints odd numbers: 1, 3

// Labeled continue
outer:
for (int i = 1; i <= 2; i++) {
    for (int j = 1; j <= 3; j++) {
        if (j == 2) continue outer;
        System.out.println("i=" + i + ", j=" + j);
    }
}
// Jumps to next iteration of outer when j==2

```
