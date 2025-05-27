# Operators

### Operator Precedence

| Operator | Symbols and examples |
|----------|----------|
| Post-unary operators | expression++, expression--   |
| Pre-unary operators      | ++expression, --expression   |
| Other unary operators    | 1-, !, ~, +, (type)   |
| Multiplication/division/modulus | *, /, %  |
| Addition/subtraction| Addition/subtraction  |
| Shift operators   | <<, >>, >>>   |
| Relational operators  | <, >, <=, >=, instanceof |
| Equal to/not equal to | ==, != |
| MShort-circuit logical operators| *, /, %  |
| Logical operators| &, ^, | |
| Short-circuit logical operators | &&, || |
| Ternary operators  | boolean expression ? expression1 : expression2|
| Assignment operators | =, +=, -=, *=, /=, %=, &=, ^=, |=, <<=, >>=, >>>= |

###  Operator Categories & Examples

### Unary Operators
* Sign: +, -
```java
int x = +5;     // x = 5
int y = -x;     // y = -5
```

* Increment/Decrement: ++i, i++, --i, i--
```java
int i = 2;
int pre = ++i;  // pre = 3, i = 3
int post = i++; // post = 3,  i = 4
```

* Logica NOT: !
```java
boolean b = !(5 > 3); // b = false
```

* Bitwise NOT: ~
```java
int bits = ~0b0101;   // bits = ...11111010 (two’s complement)
```

* Cast: (Type)
```java
double d = 9.7;
int n = (int)d;       // n = 9 (truncation)
```

* instanceof
```java
Object s = "abc";
boolean isString = s instanceof String; // true
```

### Arithmetic Operators
* Binary: *, /, %, +, -
```java
int a = 7 / 2;   // a = 3
int m = 7 % 2;   // m = 1
double f = 7 / 2.0; // f = 3.5
```
## Shift Operators
** Left shift: <<

** Signed right shift: >>

** Unsigned right shift: >>>
```java
int sh = 1 << 3;       // 8
int ri = -8 >> 2;      // -2 (sign bit replicated)
int ru = -8 >>> 2;     // large positive (logical fill with zeros)
```

## Relational / Comparison
* Numeric: <, >, <=, >=

* Equality: ==, != (primitives & references)

* Type check: instanceof

```java
String s1 = "hello", s2 = new String("hello");
boolean eqRef = (s1 == s2);       // false (different objects)
boolean eqVal = s1.equals(s2);    // true  (same content)
```

## Bitwise & Logical
* Bitwise on ints/longs: &, |, ^

* Logical (short-circuit) on booleans: &&, ||

* Non-short-circuit logical on booleans: &, |, ^

```java
boolean b1 = false, b2 = true;
boolean x1 = b1 & b2;   // false; evaluates both
boolean x2 = b1 && b2;  // false; skips b2
```

## Ternary Operator
```java
int high = 10, low = 5;
int max = (high > low) ? high : low; // max = 10
```

1. Type rules: both branches must resolve to a compatible type (primitive promotions or common reference).

### Assignment Operators
* Simple: =

* Compound: +=, -=, *=, /=, %=, <<=, >>=, >>>=, &=, ^=, |=
```javabyte b = 5;
b += 3;    // OK: implicit cast back to byte
// b = b + 3; // COMPILE ERROR: b+3 is int → needs explicit cast
```


## Precedence & Associativity
1. Highest precedence → 13. Lowest

2. Associativity: most are left-to-right, except unary, cast, assignment, and ternary which are right-to-left

## Numeric Promotion & Conversion Rules
### Binary arithmetic (+ - * / %)

1. If any operand is double → both to double.

2. Else if float → both to float.

3. Else if long → both to long.

4. Else → both to int.

```java
byte bb = 2; short ss = 3;
int sum = bb + ss;        // bb, ss promoted to int
```
### Unary + - ~ and shifts
* byte, short, char → promoted to int first.

* Shift count masked: 
    1. int: count & 0x1F (i.e. mod 32) 
    2. long: count & 0x3F (mod 64)

```java
long L = 1L;
L <<= 65; // 65 & 63 = 1 → L = 2
```

### Compound assignment
```java
short s = 5;
s += 10;  // OK: implicit cast back to short
// s = s + 10; // Compile error: s+10 is int
```

### Autoboxing / Unboxing
```java
Integer I = 5;        // boxing
int i = I;            // unboxing
// But: Integer N = null; int i2 = N; // NullPointerException
```
### Ternary (?:) Detailed Rules
```java
ResultType r = condition
               ? exprIfTrue
               : exprIfFalse;
```

* condition must be boolean.
* Result type determination:

    1. Both expressions same primitive → that type.

    2. One primitive + its wrapper → boxing/unboxing.

    3. Two different primitive wrappers → unboxed, then promote.

    4. Two reference types → nearest common supertype.

    4. Incompatible types → compile error.

```java
// OK: both String
String s = true ? "yes" : "no";

// ERROR: no common type between String and Integer
Object o = true ? "hello" : 42; 
// → must cast: true ? "hello" : (Object)42
```

## Common Exam Pitfalls & Gotchas
1. Operator precedence vs evaluation order
```java
int a = 1, b = 2;
a += b *= 3;  // b = b * 3 = 6; then a += 6 → a = 7
```
2. Integer overflow wraps silently
```java
int ov = Integer.MAX_VALUE + 1;  // ov = Integer.MIN_VALUE
```
3. char promotion
```java
char c = 'A'; 
int i = c - 1; // c→65, so i=64
```
4. Postfix vs prefix in complex expressions
```java
int i = 0;
int j = i++ + ++i;  
// Evaluate: (i++) yields 0, then i→1; (++i) yields 2; → j = 0 + 2 = 2
```
5. Shift count masking
```java
int x = 1 << 33;  // 33 & 31 = 1 → x = 2
```

6. Mixing signed and unsigned comparisons
```java
long L = -1;
// careful: L >>> 32 shifts into positive range
```

7. Boolean bitwise vs logical
```java
boolean bb = true, ff = false;
boolean r1 = bb & (10/0 > 0);  // throws ArithmeticException
boolean r2 = bb && (10/0 > 0); // short-circuit, no exception
```

8. Ternary with boxing surprises
```java
Long L1 = 0L, L2 = 1L;
long res = true ? L1 : L2;  
// auto-unboxed → long res = (long)L1; fine.
```