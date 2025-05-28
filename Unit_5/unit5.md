# Core Java APIs

## Working with Strings

### Immutability & Interning
* Strings are immutable: any “modification” creates a new object.
* String literals are interned in the String Pool; identical literals share one instance.

```java
String s1 = "Java";
String s2 = "Java";
System.out.println(s1 == s2); // true (same interned object)
```

1.2 Common Constructors & Methods

```java
// Constructors
String empty      = new String();
String fromChar   = new String(new char[]{'a','b'}); // "ab"
String fromBytes  = new String(new byte[]{65,66});   // "AB"

// Length & char access
String st = "Hello";
int len = st.length();       // 5
char c = st.charAt(1);       // 'e'

// Substring, index, contains
String sub = st.substring(1,4);       // "ell"
int idx = st.indexOf('l');            // 2
boolean has = st.contains("ll");      // true

// Case conversion & trim
String up = st.toUpperCase();         // "HELLO"
String t  = "  hi  ".trim();          // "hi"

// Replace
String r1 = st.replace('l','x');      // "Hexxo"
String r2 = st.replaceAll("\\d","*"); // regex-based
```

### String Concatenation

* Using + or String.concat().

* At compile time, literals are merged; at runtime, concatenation uses a hidden StringBuilder.

## Common Pitfalls
* == vs equals(): use equals() to compare content.

* Null safety: calling any method on null causes NPE.

* Substring shares underlying char array (pre-Java 7u6); not in modern JDKs.

# StringBuilder
## Mutability & Use-Cases

* Designed for efficient, mutable string manipulation.

* Not synchronized (unlike StringBuffer).

## Key Methods 
```java
StringBuilder sb = new StringBuilder();
sb.append("Java");            // "Java"
sb.append(123).append(true);  // "Java123true"
sb.insert(4, " SE ");         // "Java SE 123true"
sb.replace(0,4,"Kotlin");     // "Kotlin SE 123true"
sb.delete(6,9);               // remove " SE" → "Kotlin123true"
sb.reverse();                 // "eurt321niltok"
int cap = sb.capacity();      // default 16, grows as needed
sb.setLength(5);              // truncate or pad with '\0'
String result = sb.toString();
```

> ### Pitfalls
> * toString() must be called to get an immutable String.
> * Capacity vs length: capacity is the internal buffer size; length is actual characters.

## Working with Java Arrays
### One-Dimensional Arrays
Declaration & Instantiation
```java
int[] ints1;                    // declaration only
ints1 = new int[3];             // [0,0,0]
String[] strs = new String[]{"a","b","c"};
double[] d2 = {1.1, 2.2, 3.3};  // shorthand
```
Access & Looping
```java
ints1[0] = 10;                  // valid
// ints1[3] = 4;                // ArrayIndexOutOfBoundsException

for(int i = 0; i < ints1.length; i++) {
    System.out.println(ints1[i]);
}

// Enhanced loop
for (String s : strs) {
    System.out.println(s);
}
```

> ### Pitfalls
> * length is a field (no ()), e.g. array.length.
> * default values: numeric → 0, boolean → false, object refs → null.

## Two-Dimensional Arrays
Declaration & Instantiation
```java
int[][] matrix = new int[2][3]; // 2 rows, 3 columns, all zeros
// Jagged arrays
String[][] jagged = new String[2][]; 
jagged[0] = new String[1];
jagged[1] = new String[2];
```
Initialization & Access
```java
int[][] m2 = {
    {1,2,3},
    {4,5,6}
};

for (int i = 0; i < m2.length; i++) {
    for (int j = 0; j < m2[i].length; j++) {
        System.out.print(m2[i][j] + " ");
    }
    System.out.println();
}
```

> Pitfalls
> * Rows may be of unequal length in jagged arrays.
> * matrix.length is number of rows; matrix[i].length is columns in row i.

# Programming Abstractly Through Interfaces: List & ArrayList
## List Interface
* Part of java.util; ordered collection, allows duplicates, indexed access.

* Useful methods:
```java
List<String> list = new ArrayList<>();
list.add("a");            // add at end
list.add(0, "b");         // insert at index
String val = list.get(1); // retrieve
list.set(1, "c");         // replace
list.remove("a");         // by element
list.remove(0);           // by index
int size = list.size();
boolean empty = list.isEmpty();
```

## ArrayList Class
* Resizable array implementation of List.

* Not synchronized; use Collections.synchronizedList(...) for thread safety.

Constructors
```java
List<Integer> a1 = new ArrayList<>();        // default capacity 10
List<Integer> a2 = new ArrayList<>(20);      // initial capacity 20
List<Integer> a3 = new ArrayList<>(a1);      // copy existing
```
Iteration
```java
for (Integer i : a1) {
    System.out.println(i);
}

// Using iterator
Iterator<Integer> it = a1.iterator();
while (it.hasNext()) {
    System.out.println(it.next());
}

// forEach with lambda
a1.forEach(e -> System.out.println(e));
```

> Pitfalls
> * Concurrent modification: modifying during iteration (except via iterator’s remove()) throws ConcurrentModificationException.
> * subList() returns a view—changes reflect in the original list.
> * remove(Object) vs remove(int index): beware of autounboxing ambiguity with List<Integer>.

## Key Pitfalls & Exam Tips
1. String vs StringBuilder
    * Don’t expect StringBuilder to be immutable.

2. Array default values
    * Always know what uninitialized slots hold.

3. Jagged array lengths
    * Access matrix[i].length, not assume uniform columns.

4. List remove overload
```java
List<Integer> nums = new ArrayList<>(List.of(1,2,3));
nums.remove(1);      // removes element at index 1 (value 2)
nums.remove(Integer.valueOf(1)); // removes element 1
```

5. Avoid new String(...) unless necessary (breaks interning).

6. Autoboxing pitfalls
    * List<Boolean> lb = new ArrayList<>(); lb.add(null); boolean b = lb.get(0); // NPE


## Working with Set
### Common implementations:

* HashSet<E>: unsorted, permits null, average O(1) operations.

* LinkedHashSet<E>: preserves insertion order.

* TreeSet<E>: sorted (implements NavigableSet), no null elements.

```java
Set<String> hs = new HashSet<>();
hs.add("apple");
hs.add("banana");
hs.add("apple");           // ignored, duplicate
System.out.println(hs);    // [banana, apple] (order not guaranteed)

Set<Integer> ts = new TreeSet<>();
ts.add(5);
ts.add(1);
ts.add(3);
System.out.println(ts);    // [1, 3, 5] (sorted)
```

>Pitfalls
>
>* A HashSet relies on equals() and hashCode(): if you mutate a field used in hashing after >insertion, lookups break.

## Working with Map
* Definition: A Map<K,V> stores key → value pairs, where each key is unique.

* Common implementations:

    1. HashMap<K,V>: unsynchronized, allows one null key, many null values.

    2. LinkedHashMap<K,V>: insertion‐order (or access‐order) iteration.

    3. TreeMap<K,V>: sorted by key, no null keys.

```java
Map<String,Integer> m = new HashMap<>();
m.put("Alice", 30);
m.put("Bob",   25);
m.put("Alice", 31);        // replaces previous value
System.out.println(m.get("Alice")); // 31
System.out.println(m.containsKey("Bob"));  // true

// Iterating
for (Map.Entry<String,Integer> e : m.entrySet()) {
    System.out.println(e.getKey() + " → " + e.getValue());
}
```
>Pitfalls
>
> * Calling get() on a missing key returns null—watch for NPE when unboxing.
>
> remove(Object key, Object value) only removes if both matc

## The Math Class
* Utility class in java.lang, all methods are static.

* Key methods:
    1. Math.abs(x), Math.max(a,b), Math.min(a,b)

    2. Math.ceil(x), Math.floor(x), Math.round(x)

    3. Math.sqrt(x), Math.pow(x,y)

    4. Trigonometric: Math.sin(x), Math.cos(x), Math.tan(x)

    5. Random: Math.random() → double in [0.0,1.0).

```java
double v = -3.7;
System.out.println(Math.abs(v));       // 3.7
System.out.println(Math.ceil(v));      // -3.0
System.out.println(Math.floor(v));     // -4.0

int a = 5, b = 12;
System.out.println(Math.max(a,b));     // 12
System.out.println(Math.pow(a, 2));    // 25.0

double r = Math.random();
System.out.println(r);                 // e.g. 0.472853...
```

>Pitfalls
>
>* Math.random() is not cryptographically secure.
>
>* Beware floating‐point precision when comparing results.