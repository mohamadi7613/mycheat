
# Java

+ java  developed by Sun Microsystems (now owned by Oracle Corporation)
+ Today, the Java compiler is written in Java, while the JRE is written in C.
+ Syntax similar to C/C++ but without complex features like pointers
+ java follows the principle of "Write Once, Run Anywhere" (WORA)
+ Java code compiles to bytecode, not machine code. This bytecode runs on Java Virtual Machine (JVM)
+ Automatic memory management (Garbage Collection)
+ Advantages: 1. Multithreaded 2. safe (no pointer)
+ Cons:        1. Startup Time 2. Memory Consumption 3. Slower than  cpp 4. Verbose (More code required)  

## Java Version History

Java 1.0 (1996) - Initial release
Java 5 (2004) - Generics, annotations, auto-boxing
Java 8 (2014) - Lambda expressions, Stream API
Java 11 (2018) - LTS version, new features
Java 17 (2021) - Current LTS version
Java 21 (2023) - Latest LTS version

### Java Frameworks

1. Web	        Spring, Spring Boot, JSF, Struts
2. ORM	        Hibernate, JPA, MyBatis
3. Testing	    JUnit, TestNG, Mockito
4. Build	    Maven, Gradle, Ant

## Java Technologies

+ Spring Framework - Comprehensive framework for enterprise Java
+ Spring Boot - Simplifies Spring application setup
+ Hibernate - Object-Relational Mapping (ORM) tool
+ Jakarta EE - Enterprise edition specifications
+ Servlets & JSP - Traditional web technologies
+ Spring MVC - Model-View-Controller framework
+ JSF - JavaServer Faces for component-based UI
+ Maven - Dependency management and build tool
+ Gradle - Flexible build automation tool

## Java Ecosystem

1. Java SE      standard Edition            Core Java, standard libraries like collections, xml, applet, jdbc
2. Java EE      Enterprise Edition          Enterprise apps, Servlets, EJB, websocket
3. Java FX                                  GUI apps (Merged to Java SE 8)
4. Java ME      Micro Edition               Mobile / IoT, libs like Wireless Messaging

### Java implementations

+ they are different JVM and JDK, not the language itself
+ "Java implementations" usually refers to different vendors
+ 
+ Oracle JDK (main jdk)
+ OpenJDK  (Fully open-source)
+ Amazon Corretto
+ Eclipse Temurin (Adoptium)
+ Microsoft Build of OpenJDK
+ Red Hat OpenJDK
+ IBM Semeru


## Java Architecture
+ download java from oracle
+ java stands by "WORA": Write Once, Run Anywhere

1.  JDK = Java Development Kit
    JDK = JRE + Development Tools (javac, debugger, documentation tools, etc.)
    JDK = as a developer you install JDK, but for running the java program you just need JRE.
 
3.  JRE = Java Runtime Environment
    JRE = JVM + Java Class Libraries (additional to JVM we need some othre files to run the code)
    JVM is part of JRE, java program runs inside JRE and JVM is responsible for running that

1.  JVM = Java Virtual Machine
    JVM = Executes Java bytecode in platform independence
    JVM = JVM itself is not platform independent, but our application is platform independent

```bash
java --version
javac --version           # inside /bin folder of winodows
jshell                    # jshell prompt like python shell for writing simple codes like adding 2 number (2+3)

# How to run java program?     # two steps  (use && for combination in one line)
javac MyProgram.java          # 1. generates MyProgram.class
java MyProgram                # 2. runs MyProgram    (do not use .class since java command expects a class name, not a filename)
# we can do it in one step:     # java MyProgram.java        # not recommended
```


```
+------------------+       +------------------+       +------------------+        +-------+         +-----------+
|  Java Source     |       |   Compiler       |       |   JVM            |         | OS   |         |  Hardware  |
|  MyProgram.java  | --->  |  javac           | --->  |  java MyProgram  |  --->   |      |  -->    |          |
+------------------+       +------------------+       +------------------+         +-------+        +-----------+
                                   │                           │
                                   ▼                           ▼
                           MyProgram.class (bytecode)     Runs bytecode

            ┌────────────────────────┐
            │   1. Write Code         │
            │  (MyProgram.java)       │     You write Java source code in a .java file using a text editor or IDE
            └──────────┬──────────────┘
                       │
                       ▼
            ┌────────────────────────┐
            │   2. Compile Code       │
            │  javac MyProgram.java   │     The javac compiler checks syntax and converts Java code into bytecode (.class).
            └──────────┬──────────────┘
                       │
                       │ (creates bytecode)
                       ▼
            ┌────────────────────────┐
            │   3. Bytecode File      │      .class file contains bytecode
            │  (MyProgram.class)      │       Platform-independent intermediate code understood by the JVM
            └──────────┬──────────────┘
                       │
                       ▼
            ┌────────────────────────┐
            │   4. JVM Loads Class    │        The ClassLoader loads the .class file into JVM memory.
            │   (ClassLoader)         │
            └──────────┬──────────────┘
                       │
                       ▼
            ┌────────────────────────┐
            │   5. Bytecode Verified  │
            │   (Bytecode Verifier)   │         JVM verifies code safety (no illegal memory access, etc.).
            └──────────┬──────────────┘
                       │
                       ▼
            ┌────────────────────────┐
            │   6. JIT Compilation    │           The Just-In-Time compiler translates frequently 
                                                    used bytecode into native machine code for better performance.
            │   (Just-In-Time Compiler)│
            └──────────┬──────────────┘
                       │
                       ▼
            ┌────────────────────────┐
            │   7. Execution (JVM)    │             The JVM executes the bytecode.
            │   (Interpreter + JIT)   │              The JVM executes bytecode using the interpreter or JIT-compiled native code.
            └──────────┬──────────────┘
                       │
                       ▼
            ┌────────────────────────┐
            │   8. Output / Result    │
            │   (Console / GUI / API) │                The program produces output to console, GUI, file, or network.
            └────────────────────────┘


+-----------------------------------------------------------+
|                        JDK                                 |
|   -------------------------------------------------------  |
|   |                 JRE                                  | |
|   |   -----------------------------------------------    | |
|   |   |              JVM                             |   | |
|   |   |   - Class Loader                             |   | |
|   |   |   - Bytecode Verifier                        |   | |
|   |   |   - Interpreter / JIT Compiler               |   | |
|   |   |   - Garbage Collector                        |   | |
|   |   -----------------------------------------------    | |
|   |                                                      | |
|   |   • Core Java Libraries (java.*, javax.*, etc.)      | |
|   |   • Java Class Files (.class)                        | |
|   |   • Supporting Files (security, config)              | |
|   -------------------------------------------------------  |
|                                                            |
|   • Developer Tools (in addition to JRE):                  |
|       - javac (Java Compiler)                              |
|       - javadoc (Documentation Tool)                       |
|       - jar (Archiver)                                     |
|       - jdb (Debugger)                                     |
|       - other command-line utilities                       |
+-----------------------------------------------------------+



+---------------------------------------------+
|                 JVM                         |
|---------------------------------------------|
|  Class Loader Subsystem                     |
|---------------------------------------------|
|  Runtime Data Areas:                        |
|   • Method Area (class info)                |
|   • Heap (objects)                          |
|   • Stack (method calls, local vars)        |
|   • PC Register                             |
|   • Native Method Stack                     |
|---------------------------------------------|
|  Execution Engine:                          |
|   • Interpreter                             |
|   • JIT Compiler                            |
|   • Garbage Collector                       |
+---------------------------------------------+
```


### Java system memory level

1. Stack Memory
    a. Follows LIFO (Last In, First Out)
    b. stack is key-value structure
    c. stores:
        - Method calls
        - Local variables
        - Function call order
    d. every method has its own stack
    e. Each thread has its own stack
    f. Fast memory access
    g. Automatically cleaned when a method finishes (No garbage collection needed)

1. Heap
    a. The heap stores:
        - Objects
        - Arrays
        - Instance variables
    b. All threads share one heap.
    c. Slower than stack
    d. Stores data that must live beyond a single method call
    e. Garbage Collector (GC) automatically cleans unused objects

```java
// 1. stack
void test() {         // When test() ends, a is removed from the stack.
    int a = 10;   // stored in stack
}
// 2. Heap
class Person {
    String name;
}
Person p = new Person();  // object stored in heap
// 3. both working together
void test() {                       // When test() finishes: p is removed from stack.
    Person p = new Person();      // p (the reference) → stored in stack
}                                 // new Person() (the object) → stored in heap
                                  // If no reference points to the Person object anymore, GC will remove it later.
```

### Reference variable

+ In Java, “object” and “reference variable” are two different things
1. Object:
    - An object is a real instance
    - created in heap memory 
    - Created using the "new" keyword like arrays, objects
    - Contains the actual data and methods
    - 
2. reference variable:
    - A variable stores the memory address of the "object"
    - Lives in stack memory
    - Declared with a specific type
    - Points to an object in the heap

```java
String str;                    // Declaration of reference variable
str = new String("Hello");       // Creation of object and assignment to reference variable
// in one line
Person p = new Person();        // new Person() → creates an object in the heap
                                // p → is not the object, it's only a reference to it
// for bellow diagram
class Student {
    String name;
    int age;
    
    // Constructor
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Method
    public void display() {
        System.out.println("Student: " + name + ", Age: " + age);
    }
}
```

```text
Reference Variable      Object                   # Stack stores references
[ str ]    ---->    [ "Hello" ]                  # Heap stores objects
 (Stack)             (Heap)                     # Reference points to object by memory address.


STACK MEMORY (Method Area)      HEAP MEMORY (Object Storage)
┌────────────────────────┐     ┌─────────────────────────────┐
│                        │     │                             │
│  REFERENCE VARIABLES   │     │         OBJECTS             │
│                        │     │                             │
├────────────────────────┤     ├─────────────────────────────┤
│ student1 ──────────────┼─────┤ Student Object #1001        │          Student student1 = new Student("John", 20);
│   [0x1001]             │     │ ┌─────────────────────────┐ │
│                        │     │ │ name: "John"            │ │
│                        │     │ | age: 20                 │ │
│                        │     │ │ methods: ...            │ │
│                        │     │ └─────────────────────────┘ │
│ arr ───────────────────┼─────┤                             │
│   [0x2001]             │     │ int[] Array #2001           │         int[] arr = {10, 20, 30, 40, 50};
│                        │     │ ┌─────────────────────────┐ │
│                              │ │ [0] [1] [2] [3] [4]     │ │
│                        │ │   │ │  10  20  30  40  50     │ │
│                        │ │   │ └─────────────────────────┘ │
└────────────────────────┘ │   │                             │
                               └─────────────────────────────┘
```




## Basic
+ our code without "signature" not works, it gives error
+ jvm looks for this particluar signature to run the code
+ name of the file should be same as the name of the class
+ is better to use CamelCase for class names
+ MAIN METHOD is an ENTRY POINT

```java
// signature: 1. public 2. class 3. Main                       // 1. public 2. static 3. void 4. main 
// public static void main(String[] a) is Entry point of every Java program                                      
//  Main.java                                           // public means this class is accessible from other classes
public class Main {                                  // 'Main' is the name of our class (should match the filename: Main.java)
    public static void main(String[] args) {         // 'static': belongs to the class rather than any object instance
        System.out.println("Hello, World!");        // 'void': doesn't return any value
    }                                              // The main method is the entry point 
}                                                 // 'String[] args': parameter that accepts command-line arguments
// there is no need to create an object for calling the main() in class Main, by default it is the entry point
```

## comments

```java
// single line
/* multi line */
/** documentation comment */
```

### Declaration and assignment statements

+ operands = a, b
+ operators = + - * / %
+ expersion = a + b

```java
int num;             // declaration statement
num = 10;                 // assignment statement
int c = a + b;            // inline initialization statement
a = 12;                 // a is variable and 12 is literal
int a=1, b=2, c=3;    // multiple variable declaration
```

### scope of variables

1. Local Variables
    - Declared inside a method, constructor, or block
    - Destroyed when block/method ends
    - Cannot have default values—you must initialize them
    - 
2. Instance Variables
    - Declared inside a class but outside methods
    - Belong to an object
    - Each object has its own copy
    - Have default values (e.g., 0, null, false)
    - 
3. Static (Class) Variables
    - Declared with the static keyword inside a class
    - Shared by all objects
    - Stored in method area of memory
    - Have default values
    - 
4. Block Scope
    - kind of local variable that defines inside {}
5. Global variable
    - there is no true “global variable” like C in java

```java
class User {
    String name;            // instance variable
    static int count = 0;   // static variable
}
```

### Constants

```java
final double PI = 3.14159;
final int MAX_SIZE = 100;
```


### Access Modifiers

### operators
1. arithmetic operators               + - * / % ++ --   
    - addition, subtraction, multiplication, division, modulus(remainder)
    - increment and decrement operators  ++ --
2. relational operators               > < >= <= == !=
3. logical operators                  && || !
    a. truth table 
4. bitwise operators                  & | ^ ~ << >>
5. assignment operators                 = 
    a. compound assignment operators     += -= *= /= %=   
    b. Bitwise Compound Operators        &= |= ^= <<= >>= >>>=
6. Unary operators (One-operand operations)     ++ -- + - !
7. ternary operator                  ? :

#### compound assignment operators

```java
int num = 10;
num += 10;
num -= 10;
num *= 10;
num /= 10;
num %= 10;
num &= 10;
```

### Short-Circuit Evaluation

+ when we have logical operators like && and ||, Java stops evaluating a logical expression as soon as the result is already known.
+ To save time and prevent unnecessary operations.
+ && stops if left is false
+ || stops if left is true
+ helps avoid errors, like division by zero or null pointer access

```java
int a = 10;
int b = 20;

boolean x = true;
boolean y = false;

if ( a > 1 || b <45) { }               // short-circuited
if (y && x) { }               // short-circuited
```


### Concatenation

+ the only operattor that works with strings is the + operator

```java
String s = 3 + "str" + 3;     // 3str3
String s = 3 + 3 + "str";     // 6str
```


### parantesis

+ Grouping expressions

```java
int i = 10; 
System.out.print("" + i + 5);     // 105
System.out.print("" + (i+5));    // 15
```

### Bitwise operators

```java

```

### Unary Operators

+ Operate on a single operand.

```java
int p = num++;                   // post increament operator     // num is 11, p is 10  // first fetch the value then increament
num--;                   // post decreament operator    // we can write in one statement
int p = ++num;                     // pre increament operator    // num is 11, p is 11  // first increament then fetch

int a = 10;
+a;         // +10   // Unary plus
-a;         // -10   // Unary minus -> negates value
++a;         // 11    // Pre-increment
a++;          // 11   // Post-increment
!a;           // Error  // Logical NOT
```

### Primitive Data Types

+ Everything in Java is an object (except primitive types)
+ A literal is a fixed value written directly in the code. like `int x = 123;`
+ 1 byte = 8 bits 
+ 1 bit = 0 or 1

| Type    | Size    | Example           |
| ------- | ------- | ----------------- |
| byte    | 1 byte  | byte b = 10;      |
| short   | 2 bytes | short s = 100;    |
| int     | 4 bytes | int x = 123;      |
| long    | 8 bytes | long l = 123L;    |
| ------- | ------- | ----------------- |
| float   | 4 bytes | float f = 1.23f;  |
| double  | 8 bytes | double d = 1.23;  |
| ------- | ------- | ----------------- |
| char    | 2 bytes | char c = 'A';     |
| boolean | 1 bit   | boolean b = true; |

```java
byte smallNumber = 100;        // 8-bit integer                        // (-128, 127)
short mediumNumber = 1000;     // 16-bit integer                       // (-32,768, 32,767) 
int number = 100000;           // 32-bit integer (most common)            // (-2,147,483,648, 2,147,483,647)
long bigNumber = 1000000000L;  // 64-bit integer                        // (-9,223,372,036,854,775,808, ..)

float decimal = 3.14f;                  // 32-bit floating point                   // ~6–7 digits precision
double preciseDecimal = 3.1415926535;  // 64-bit floating point           // ~15–16 digits precision 

char letter = 'A';             // Single character in Unicode (not ASCII like C)  // double quotes gives error
boolean flag = true;           // true or false
```

### Non-Primitive Types

+ Non-Primitive Data Types:   String, Arrays, Classes, Interfaces, Enums
+ primitive data types starts with small letters but Non-Primitive data types have capital letters

```java
String name = "Java Programming";  // Sequence of characters
int[] numbers = {1, 2, 3, 4, 5};  // Array
Object obj = new Object();         // Any Java object
```


### Double and Float Precision

+ double is default data type in java

```java
int a = 0b101;                          // binary format   // 010 is binary = 5 is decimal   // 'b' or 'B' suffix required
int b = 0x7E;                           // hex format        // 7E is binary = 126 is decimal
int c = 0o123;                          // octal format      // 123 is binary = 83 is decimal
int d = 10_000_000;                     // underscores in numeric literals for readability in development 
System.out.println(d);                  // output: → 10000000   d is equal to 10000000
float decimal = 3.14;                   // Error 'f' or 'F' suffix required
float f = 1.123456789f;                  // by default  3.14 is double and 3.14f is float
double a = 45;                            // integers are converted to doubles: 45.0
double a = 12e5;                          // 12 * 10^5 = 120000
double d = 1.123456789;

System.out.println(f); // → 1.1234568
System.out.println(d); // → 1.123456789

// increment and decrement operators
char c= 'c';
c++;
System.out.println(c);      // output: d
```

### Floating-point exeptions

```java
3.141 + 2.0
3.141 - 2.0
3.141 / 2.0
```

### Boolean

```java
// STATIC METHODS
Boolean.parseBoolean("true");      // parse string to boolean
Boolean.valueOf("false");     // string → Boolean object
Boolean.toString(true);            // boolean → String
Boolean.compare(true, false);         // compare two booleans (1 if first is true, 0 if equal, -1 if first false)
Boolean.equals(Boolean.TRUE);      // compare Boolean objects

// INSTANCE METHODS
Boolean flag = true;
flag.booleanValue();              // Boolean object → primitive
flag.toString();                     // Boolean object → String
flag.compareTo(Boolean.FALSE);      // compareTo returns 1,0,-1
```


### wrapper class
+ A wrapper class in Java is a class that "wraps" a primitive data type into an object
+ Wrapper classes are immutable
+ why we need wrapper class?
    - Collections (like ArrayList) cannot store primitives directly.
    - Autoboxing/unboxing allows automatic conversion between primitive and object.

```java
// PRIMITIVES AND THEIR WRAPPERS
// byte      → Byte
// int       → Integer
// float     → Float
// char      → Character
// boolean   → Boolean

import java.util.*;

public class Main {
    public static void main(String[] args) {
        // Autoboxing: primitive → wrapper
        Integer num = 10;  

        // Unboxing: wrapper → primitive
        int n = num;        

        // Using wrapper method
        int parsed = Integer.parseInt("42");  // string → int

        // Storing primitives in a Collection
        ArrayList<Integer> list = new ArrayList<>();
        list.add(5);    // primitive automatically boxed to Integer
        list.add(num);

        System.out.println("List: " + list);
        System.out.println("Parsed number: " + parsed);
    }
}
```

### Number Static Methods

```java
// 1. Static methods             // static methods are very similar for int, float, double ,...
Integer.parseInt("42");          // Converts a string to int	
Double.parseDouble("3.14");      // Converts string to double	
Integer.valueOf("10");           // string to Integer object
Integer.toString(123);           // Converts number to string	
Integer.toHexString(255);             // → "ff"              // Converts int to hex	
Integer.toBinaryString(5);           // → "101"             // Converts int to binary
Integer.toOctalString(8);           // → "10"               // Converts int to octal
Integer.compare(3, 5);            // → -1                   // Returns -1, 0, or 1	
Integer.sum(2, 3);                  // → 5                         // Returns x + y	

// 2. Instance methods           // instance methods are very similar for int, float, double ,...
Integer obj = 10;
obj.intValue();        // Integer to int
obj.doubleValue();  // Integer to double
obj.floatValue();    // Integer to float
obj.longValue();      // Integer to long

// 3. MATH CLASS METHODS
Math.pow(2,3);            // -> 8
Math.abs(-5);           // → 5
Math.max(3, 7);         // → 7
Math.min(3, 7);         // → 3
Math.sqrt(9);           // → 3
Math.cbrt(27);          // → 3
Math.ceil(3.14);        // → 4
Math.floor(3.14);       // → 3
Math.round(3.14);       // → 3
Math.sin(0);            // → 0
```

### Char
+ use single qoute for characters and double qoutes for strings

```java
char x = "A";                       // error
String x = 'a string';              // error
char ch = '\u0041';                 // A       Unicode escape
char ch = 65;                       // A       ASCII Unicode
ch++;                               // becomes 'B'  // Character Operations
char nl = '\n';                     // '\n', '\t', '\r' '\b' and so on are Special Characters (Escape Sequences)

// Character Class (Wrapper)
Character.isLetter('A');            // → true
Character.isDigit('5');             // → true
Character.isWhitespace(' ');        // → true
Character.isUpperCase('A');         // → true
Character.isLowerCase('a');         // → true

Character.toUpperCase('b');         // → 'B'
Character.toLowerCase('H');         // → 'h'
Character.toTitleCase('ǳ');         // → 'ǲ'

Character.getNumericValue('9');     // → 9
Character.getNumericValue('A');     // → 10
Character.digit('F', 16);           // → 15
Character.toString('A');            // → "A"
```

### Strings


```java
// STRING CREATION
String s1 = "Hello";                   // String literal
String s2 = new String("World");       // Using constructor (string is non-primitive data type)

// LENGTH & ACCESS
s1.length();                 // length of string
s1.charAt(1);                // character at index 1
s1.indexOf('l');             // first occurrence of 'l'
s1.lastIndexOf('l');     // last occurrence of 'l'

// COMPARISON
s1.equals("Hello");       // true if content matches
s1.equalsIgnoreCase("hello"); // case-insensitive
s1.compareTo("World");       // lexicographic comparison
s1.contains("ell"); // true if substring exists
s1.startsWith("He");  // true if starts with substring
s1.endsWith("lo");      // true if ends with substring

// MODIFICATION
s1.toUpperCase();       // convert to uppercase
s1.toLowerCase();       // convert to lowercase
s1.replace('l', 'x'); // replace char
s1.replaceAll("l", "x"); // replace regex
s1.substring(1, 4);       // substring from index 1 to 3
s1.trim();            // remove leading/trailing spaces
s1.concat(" World");   // concatenate strings
```

### String Pool

+ String literals are stored in String Pool
+ The String Pool is a special memory area inside the Java heap
+ Because strings are used a lot and often repeated (performance), they are stored in String Pool
+ a and b point to the same memory location
+ == compares memory addresses, not text
+ Why Strings must be immutable?
    - If a string in the pool changed all references to it would unexpectedly change

```java
String a = "a";                             // saved in String Pool
String b = "a";                            // saved in String Pool
String s2 = new String("a");             // Strings are object 
String s3 = new String("a");             // This always creates a new object in the heap (not the pool)
String s4 = new String("a").intern();     // forcing String to be stored in String Pool
System.out.println(s2 == s3);             // false        // compares memory address
System.out.println(a == s3);             // false       // compares memory address
System.out.println(a == b);             // true        // compares memory address
System.out.println(a.equals(s3));        // true        // checks if content matches

// immutable String
String s = "Hello";                        // strings are immutable
s = s + " World";                        // This (concatenation) does not modify the original string in heap  --> It creates a new object in heap

//  Heap (String Pool)
//  -------------------------------
//  "Hello"  ← s                        // does not modify the original string in heap
//  "Hello World"  ← new value          // creates a new object in heap (add new record)
```


### String Builder and String Buffer

+ StringBuilder and StringBuffer are mutable
+ StringBuilder is faster than StringBuffer
+ StringBuffer is not thread safe but StringBuilder is
+ You can change their content without creating new objects
+ use these when you want to manipulate strings

```java
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");                                // Only one object exists, modified in place
sb.delete(6, 11);                                   // Delete from index 6 to 10
sb.toString();                                     // Convert to String

//  Heap
//  -------------------------------
//  "Hello"  ← sb   ---> in the same place "Hello World"
```


### Special values

1. NaN
2. Infinity
4. Null
5. Max and Min value


```java
// 1. NaN
double nan1 = Double.NaN;      // creates NaN
double nan2 = 0.0 / 0.0;        // Results in NaN
double nan3 = Math.sqrt(-1);    // Results in NaN
Double.isNaN(nan1);              // checking for NaN
nan1 == nan2;                    // Always false

// 2. Infinity
double posInf1 = Double.POSITIVE_INFINITY;
double posInf2 = 1.0 / 0.0;
double negInf1 = Double.NEGATIVE_INFINITY;
double negInf2 = -1.0 / 0.0;
Double.isInfinite(posInf1);           // checking for Infinity

// 3. Max and Min value
Integer.MAX_VALUE;
Integer.MIN_VALUE;
Long.MAX_VALUE;
Double.MAX_VALUE;
Double.MAX_VALUE+ 1;                // overflow: Wraps around to MIN_VALUE

// 4. Null
String str = null;
Integer number = null;
if (str == null) {}
```

### Type casting

1. Implicit Casting (Widening) – Happens automatically when a smaller type is converted to a larger type.
2. Explicit Casting (Narrowing) – must be done manually

+ we can not convert boolean to other types
+ Type Casting Hierarchy: 
    1. Widening: left → right (automatic)
    2. Narrowing: right → left (explicit cast required)

```text
byte → short → int → long → float → double
char → int → long → float → double
```

```java
byte z = 130;               // Error  // 130 is out of byte range

// 1. Implicit Casting
int i = 100;
long l = i;        // int → long (automatic)      // 100          // no data is lost
float f = l;       // long → float (automatic)    // 100.0 is float
int aa = 'A'        // char → int (automatic)      // 65
long a =  123;          // int → long (automatic) // By default, integer literals (numbers without a suffix) are treated as int in Java
long t = 21321323212564;       // Error   // add 'l' at the end

// 2. Explicit Casting
double d = 145.456;                     // 
int i = (int) d;                        // double → int (explicit)     // 145   Rounding (loss decimals)
byte b = (byte) i;                     // int → byte (explicit)       // -111   145 % 256 = -111  // 256 is range of byte (-128 to 127)
byte bb = (byte) 257;                   // 1      // 257 % 256 = 1  (% is remainder)
float f = 112.23f;
long l = (long) f;           // float → long (explicit)     // 112         // Rounding
char c = (char) aa;              // int → char (explicit)       // 'A'
long a = 12345679856564646l;       
int i = (int) a;                   // -177232442
```


### Type promotion

+ Java automatically promotes smaller types to larger types during arithmetic operations.

```java
// 1.
byte a = 10;                 // when we multiply two byte types, the result is int (not byte)
byte b = 30;
int c = a * b;
// 2.
int a = 10;
float b = 20.5f;         // byte → short → int → long → float → double
float result = a + b;   // int → float (automatic)   // 35.5
```


### Array

+ Arrays in Java are fixed size; cannot grow dynamically like python and js
+ drawbacks:
    - in memory array allocated the continuest space, so if you specify the size you cannot change it
    - Arrays are homogeneous, meaning all elements must be of the same type
    - No Built-In Methods like add(), remove(), sort()
    - Wasted Memory: If you create a large array but use only a few elements
    - Does Not Support Generic Behavior
    - for searching it will traverse between the elements
    - 
+ For dynamic arrays, consider using ArrayList.

```java

// 1D array                             // use array when you need fiexd size of values
int[] arr1 = new int[5];               // default values 0
int[] arr2 = {1, 2, 3, 4, 5};          // initialized with values
int a[] = {1};                          // cpp style
int[] a = {1};                          // java style
int[] a, b;                             // both are int arrays
int a[], b;                             // a is array, b is just int (confusing!)
System.out.print(arr1)                // [I@372f7a8d     address

// 2D array
int[][] arr2D = new int[2][3];         // 2 rows, 3 columns
int[][] arr2DInit = {{1,2,3}, {4,5,6}}; // initialized

// jagged array                          // 2D arrays can be rectangular or jagged (different column lengths).
int [][] a = new int [3][];                // jagged array
int [][] a = {{1,2,3}, {1}, {1,2}}

// ACCESS & LENGTH
arr2[0];                   // access element at index 0
arr2[1] = 10;                          // assign value at index 1
arr2.length;                 // length of 1D array
arr2D.length;               // number of rows
arr2D[0].length;            // number of columns in first row

// Loop
for(int i = 0; i < arr2.length; i++) {}      // i is columns
for(int val : arr2) {}                      // Enhanced for loop

// Methods
import java.util.Arrays;
Arrays.sort(arr2);                     // sort array
Arrays.binarySearch(arr2, 3);           // search element (array must be sorted)
Arrays.equals(arr2, arr1);              // compare arrays
Arrays.toString(arr2);                  // convert 1D array to String
Arrays.deepToString(arr2D);             // convert 2D array to String
Arrays.fill(arr1, 5);                  // fill array with value 5
```


### ArrayList

```java
// DECLARATION
import java.util.ArrayList;
ArrayList<Integer> list1 = new ArrayList<>();          // empty ArrayList
ArrayList<String> list2 = new ArrayList<>();           // empty ArrayList of Strings
ArrayList<Integer> list3 = new ArrayList<>(10);        // initial capacity 10

// ADD ELEMENTS
list1.add(5);                // add element at end
list1.add(1, 10);            // add element at index 1
list1.addAll(list2);          // add all elements from another collection

// ACCESS ELEMENTS
list1.get(0);       // get element at index 0
list1.size();      // get number of elements
list1.contains(5); // check if element exists

// MODIFY ELEMENTS
list1.set(0, 20);             // replace element at index 0
list1.remove(1);              // remove element at index 1
list1.remove(Integer.valueOf(5)); // remove element by value
list1.clear();                // remove all elements

// ITERATION
for(int i = 0; i < list1.size(); i++) {}
for(int val : list1) {}                                // Enhanced for loop
list1.forEach(n -> System.out.println(n));             // foreach


// SORTING
import java.util.Collections;
Collections.sort(list1);       // sort ascending
Collections.reverse(list1);    // reverse
Collections.shuffle(list1);    // random shuffle
```

### Enum

+ enum is a short term for enumeration
+ enum is used to define a fixed set of constant values.
+ enum in java can have methods and fields.


```java
enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}
Day d = Day.MONDAY;
Day d = "monday";         // error

// methods
Day d = Day.valueOf("MONDAY");
Day[] days = Day.values();
Day.SUNDAY.ordinal();
Day.SUNDAY.name();
for (Day d : Day.values()) {}

enum Status {
    SUCCESS(200),
    ERROR(500),
    NOT_FOUND(404);

    private int code;

    Status(int code) {   // constructor
        this.code = code;
    }

    public int getCode() {
        return code;
    }
}
```


## Input/output

```java
// 1. print
System.out.print("Hi");                    // Hi
System.out.println("Hi");                  // Hi \n
System.out.printf("Age: %d", 25);         // formatted print with placeholders -> %d %s %f %c %b %n (new line)

// 2. input
import java.util.Scanner;
Scanner sc = new Scanner(System.in);
int a = sc.nextInt();
double x = sc.nextDouble();
String s = sc.next();                      // hello          reads until space
String line = sc.nextLine();               // hello world   reads whole line (until Enter)
char c = sc.next().charAt(0);

// 3. Console Class for input
Console console = System.console();
String user = console.readLine("Username: ");
char[] pass = console.readPassword("Password: ");       // Secure way to read passwords (no echo)
```

## Conditoins

1. if
2. if-else          Use if-else for ranges or complex conditions
3. if-else-if
4. ternary          set a value per condition
5. switch case     Use switch for exact matches which is cleaner for many options
    
+ Compare strings with .equals() ->  Never use ==

```java
// 1 if-else
if (a>10) System.out.println("a is greater than 10");              // without curly braces just for one statement
else
    System.out.println("a is less than 10");                      // without curly braces

// 2 if else if
if (condition1) {
    // true → runs this
} else if (condition2) {
    // true → runs this
} else {
    // runs if all above are false
}

// 3. ternary operator       question mark + colon
result = n%2 ? 10 : 20;

// 4. switch case
int day = 3;           // 
switch (day) {         // write a variable or write an expression like (a+b), do not write condition (common mistake)
    case value1:        // swtich case can be used without break
        // code        // switch jumps to the matching case and runs the code unitl it finds a break
        break;         // so if we have no break in multiple case, all those case will be executed
    case value2:     // Without break, control falls through the next case. Use break; to stop after one match
        // code       // java 14+ does not need break and the synatx is more clean
        break;
    case value3, value4:  // Multiple values can be matched (java 14+)
    default:           // Optional but recommended
        // code
}
String result = switch (day) {}       // Switch Expressions (Java 14+)
```


## Control Flow

+ Loops let you repeat code until a condition is met.
+ i stands for iteration

1. for loop
    - when you know exactly how many times to loop 
    - file (we do not know)
    - everything in one line:
2. while
    - when you do not know how many times to loop (cindition)
3. do while
    - runs at least once (condition checked after execution)
4. Enhanced for loop (for-each)



```java
// 1. for loop
int i = 0;
for ( ;i<5; ) {                // for syntax equivalent
    System.out.println(i);
    i++;
}

// 2. while
while (condition) {
    // code to execute
}

// 3. do while
int i = 0;
do {
    System.out.println(i);
    i++;
} while (i < 5);

// 4. enhanced for
int[] nums = {10, 20, 30};        // iterate over arrays or collections
for (int n : nums) {              // return one value at a time: 10, 20, 30
    System.out.println(n);         // it knows the length of array (no exception)
}
```

### Loop Control Statements (jump statemenets)

1. break           Exits the loop completely
2. continue        Skips the rest of the current iteration
3. return          Exits the method entirely
4. Labels          we can label loops for break and continue of nested loops
5. Infinite loops  Used in servers or listeners

```java
for (int i = 0; i < 5; i++) {
    if (i == 2) continue;       // skip 2
    if (i == 4) break;          // stop loop
    System.out.println(i);    // 0 1 3 
}

// Labels
outer:
for (int i = 1; i <= 3; i++) {
    for (int j = 1; j <= 3; j++) {
        if (j == 2) break outer; // exit both loops
        System.out.println(i + " " + j);
    }
}
```

### go to statement

+ Java does not support the goto statement (use labels instead)
+ Although the word goto is a reserved keyword, it’s not implemented in the language.
+ Java keeps goto as a reserved word just to prevent confusion with older languages like C and C++.
+ Java removed goto from cpp since it makes code hard to read and debug


### block

+ A `block` is a group of statements enclosed in { }
+ Blocks are used to group statements (Execute multiple lines as one)
+ It defines a new `scope` for variables and controls execution
+ `scope` means where a variable can be accessed in the code (The permission of accessing a variable)

1. Local Block
2. Method Block
3. if Block and loop Block
4. class Block
5. static Block
6. Instance Initialization Block

###### 1. local block

+ local block is inside a method
+ it is created when a function is called

```java
void test() {                           // method block
    {                                  // local block
        int x = 10;                    // block will execute normally (it just deines a new scope)
        System.out.println(x);
    }
    // x is not accessible here
}
```

###### 2. instance block

+ An instance block is a block of code inside a class that runs every time an object is created, before the constructor.

```java
class Test {
    int x = 10;
    {                                     // instance block
        System.out.println("Hello");
    }
    staic{                               // static block
        System.out.println("Hello2");
    }
}
Test t = new Test();          // prints Hello, Hello2
```


### function

+ Functions in Java are called methods
+ Return values can be stored or used directly in expressions
+ In Java, you cannot change the order of parameters when calling a method like python -> we can ue method overloading
+ Default parameters do not exist → use overloading.
+ Overloaded methods must have different parameter lists (return type does not matter)
+ Java is strictly "call by value", but the behavior differs for primitives vs objects.


```java
// 1. return type
int add(int a, int b) {             // returnType name (parameters)
    return a + b;                   // retunr is allowed only if returnType is NOT void
}                                    // A method must return something unless it is void

// 2. non-static methods
void greet() {                         // non-static method or instance method
    System.out.println("Hello!");      // Requires an object
}


// 5. static methods
static int add(int a, int b) { return a + b; }
Main.add(1, 2);                       // call without creating an object


// 3. defautl value
// Java does NOT allow default parameter values like other languages.
// Use method overloading instead.
int sum(int a, int b) { return a + b; }
int sum(int a) { return a + 0; }    // overloaded version

// 4. method overloading                           // Same name, different parameters
int multiply(int a, int b) { return a * b; }
double multiply(double a, double b) { return a * b; }

// 6. returning myltiple values
int[] getValues() { return new int[] {1, 2, 3}; }


// 7. argument vs parameter
int add(int a, int b) { return a + b; }     // a, b are parameters
int result = add(1, 2);                     // 1, 2 are arguments

// 8. Varargs (Variable Arguments)
int sum(int... numbers) { return Arrays.stream(numbers).sum(); }    // Varargs allow variable number of arguments of the same type
sum(1);
sum(1,2,3);

void printAll(Object... items) {}        // Using Object... for mixed types

// 9. final parameters
void processFinal(final int x, final String name) {         // Final parameters cannot be reassigned within the method
    // x = 10;  // Compilation error - cannot assign to final parameter
    // name = "New Name";  // Compilation error
}

// 10 Effectively Final Parameters
void show(int x){                    // Parameters that are not declared final but are never modified.
    System.out.println(x);           // x is effectively final
}


// 11. Command-Line Parameters
public static void main(String[] args) {         // The special main method parameter
    System.out.println(args.length);
}

// 12. keyword arguments                        // No keyword arguments in Java like python
void greet(String name, int age) {}
// greet(name="Charlie", age=25);               // Not possible in java
// greet(age=25, name="Charlie");               // Not possible in java --> alternative way: use object for sending arguments
greet("Charlie", 25);                           // send in the exact order 


// 14. call by value
void modifyValue(int x) {
    x = x * 2;                                  // Modifies the copy, not original
    System.out.println("Inside method: " + x);  // x is 20
}
int original = 10;
modifyValue(original);                         // original is 10 (not modified)

// 15. call by reference
// java is strictly "call by value" bUT for objects, the value that gets passed is a reference, which makes it behave like call-by-reference when modifying the object, but NOT when reassigning.  --> Safe Passing Using Deep Copy
class Person {                                    // 1. create a class
    String name;                           
    Person(String name) {
        this.name = name;
    }
}
void modifyObject(Person p) {                        // 2. create a method for modifying object
    p.name = "Modified";                            // Affects original object
    System.out.println("Inside method: " + p.name);  // Modified
    
    p = new Person("New Person");                           // Doesn't affect original reference
    System.out.println("After reassignment: " + p.name);  // New Person
}
Person originalPerson = new Person("Original");          // 3. create an object
modifyObject(originalPerson);                            // 4. call the method


// 8. method chaining
```

#### return multiplt values

+ In Java, a method cannot directly return two different types at the same time
+ ways to simulate returning multiple types:
    1. Use a Class (Best Practice)
    2. Return a Pair or Tuple


```java
// 0. limitaion in java
public int func(int num){        // we cannot do this in java
    if num > 10{ return 10}
    else { return "string"}
}

// 1. Use a Wrapper class
class Result {
    int number;
    String text;

    Result(int number, String text) {
        this.number = number;
        this.text = text;
    }
}
Result test(int num) {
    if (num > 10)
        return new Result(3, null);
    else
        return new Result(null, "string");
}

// 2. Return a Pair or Tuple

import javafx.util.Pair;                       // import library
Pair<String, Integer> test(int num) {
    if (num > 10)
        return new Pair<>("number", 3);
    else
        return new Pair<>("string", null);
}
```


### shallow copy and deep copy

+ A shallow copy copies the reference of object fields — NOT the actual objects inside it.
+ A deep copy clones the object AND all inner objects (recursively).

```java

```

### Exception Handling

+ Exception is runtime error (not compile time)
+ An exception is an event that stops the normal flow of a program.
+ events like: dividing by zero, file not found, array out of bounds. 

```text
Throwable
  ├── Error                (Do NOT catch)
  └── Exception
        ├── Checked Exceptions   (Must handle)
        └── Unchecked Exceptions (Runtime exceptions)
```

#### 1. Checked Exceptions

```java
try {
    FileReader fr = new FileReader("a.txt");
} catch (IOException e) {
    System.out.println("Error");
} finally {
    System.out.println("Always executed");
}
```

#### 2. Unchecked Exceptions

```java

```


#### 3. Errors

```java

```

#### 4. Throwing Your Own Exceptions

```java

```


### Wrapper Classes

Autoboxing → primitive → wrapper
Unboxing → wrapper → primitive

```java

```

### oop

+ object:
    1. properties
    2. methods
+ class is a blueprint of object
+ JVM is responsible for creating objects in java


```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}

public class Demo{


    // public static void main(String[] args) {
    //     Demo demo = new Demo();
    //     System.out.println(demo.sum);
    //     demo.calc.add();
    // }
    public static void main(String[] args) {
        int num1 = 10;
        int num2 = 20;
        Calculator calc = new Calculator();
        int result = calc.add(1, 2);
        System.out.println(result);
    }
}

```

### static

1. static variable
2. static method
3. static block
4. static class

###### 1. static variable

+ also called a class variable
+ Exists once in memory, shared among all objects
+ Lifetime: is created when the class is loaded and exists until the program ends.
+ you can access with object, but avoid it. use staic way (using Class name)

```java
class Phone{
    int number = 10;
    static String brand = "Samsung";
}

Phone a = new Phone();
System.out.print(Phone.brand);         // accessed using the class name      // Samsung
System.out.print(a.brand);             // accessed using the object (avoid)  // Samsung

a.brand= "S1";                          // we can change the static variable using an object (avoid)
System.out.print(a.brand);             // S1  (avoid)
System.out.print(Phone.brand);         // S1    // shared among all objects

Phone.brand = "S2";                   // we can change the static variable using the class
System.out.print(a.brand);             // S2
System.out.print(Phone.brand);         // S2  
```

###### 2. static method

+ Can access only static variables and static methods directly
+ Cannot use this or super

```java
class Demo {
    int x = 5;
    static int y = 10;            // static variable

    static void test() {         // static method
        System.out.println(y);   // OK
        System.out.println(x);   // ERROR
    }
}
Demo.test();                     // call static method
Demo a = new Demo();
a.test();                       // call static method (avoid)

// How to access instance variables in static method?
class Demo {
    int x = 5;
    int y = 10;            
    static void test(obj) {                 // static method
        System.out.println(obj.y, obj.x);   // OK
    }
}
```

##### 3. static main

+ if we do not use static keyword in main, it will be a non-static method and we need to create an object to call it
+ since the main method is the entry point of the execution, we cannot call the main method using the class name (deadlock)


###### 4. static block

1. static block
    a. Object-level initialization
    b. useful for initializing static variables (constructor is not good, since it will initialize multiple times)
    c. static block is executed when the class is loaded into memory (run only once)
    d. Runs before any object is created
    e. Can access only static members
    f. Runs before instance block

1. instance block
    a. Class-level initialization
    a. instance block runs every time an object of the class is created
    b. Runs before the constructor
    c. Has access to instance members + static members


```java
class Mobile{
    int number;
    String name = "mobile";
    static String brand;
    {                                            // 1. instance block
        System.out.println("Instance block");
        System.out.println(number);              // 0 
        number = 20;                             // initialize instance variable
        System.out.println(number);              // 20
    }
    static {                                    // 2. static block
        System.out.println("Static block");
        System.out.println(brand);              // null
        brand = "Samsung";                        // initialize static variable
        System.out.println(brand);              // Samsung
    }
}

```

###### 5. static class

+ we cannot create an object from a static class

### Classes & Objects

```java
class Person {
    String name;
    Person(String name){ this.name=name; }
}
```


### Constructors

### getter and setter

### Static & Final


### OOP Concepts

Encapsulation – private fields + public getters/setters

Inheritance – extends

Polymorphism – overriding & overloading

Abstraction – abstract class, interfaces


| Modifier  | Same Class | Package | Subclass | Anywhere |
| --------- | ---------- | ------- | -------- | -------- |
| private   | ✔          | ✖       | ✖        | ✖        |
| default   | ✔          | ✔       | ✖        | ✖        |
| protected | ✔          | ✔       | ✔        | ✖        |
| public    | ✔          | ✔       | ✔        | ✔        |


Method Overloading (same name, different params)
Method Overriding (child changes parent method)

### import and export



### java.util

+ java.util is a package
    - Collections          ArrayList, LinkedList, Map, Set 
    - Date/Time            Calendar, Date, ...             ---> java.time is preferred now
    - Utility              Scanner, Random, StringTokenizer, UUID, Math

```java
import java.util.ArrayList;            // Explicit import
import java.util.*;                    // Wildcard import
```

### Generics

+ Generics also known as `type parameters` or `parameterized types`
+ Generics in Java, introduced in JDK 5
+ Generics were primarily introduced to address issues related to the Java Collections Framework
+ We have generic methods, generic classes, and generic arrays
+ generics let you create a "template" for a class or method that can work with different types without casting
+ with generics, "data types" in a method are specified as parameters at the time of usage
+ generics are like formal parameters for a method, but 
    - instead of taking values (like an integer or a string)
    - they take types (like Integer or String)

+ the <> syntax is called the diamond operator
+ it is primarily used with generics
+ Generics add type safety and remove the need for casting.
+ with generics compiler catches type errors at compile-time (not runtime)
+ You cannot use primitive types with generics - use wrapper classes
+ You cannot create generic arrays directly
+ Generic types are invariant - List<String> is NOT a subtype of List<Object>



```java
// Before Generics we have 2 problem
List a = new ArrayList();
a.add("hello");
a.add(123);                      // 1. Allowed at compile time, but cause runtime errors
String s = (String) a.get(0);   // 2. Explicit cast required

// With Generics 
List<String> a = new ArrayList<>();             // Compiler knows it holds Strings
a.add("hello");
// a.add(123);                                       //Compile-time error
String s = a.get(0);                            // Type is inferred (no casting needed)
```



#### Type inference

+ Type inference in Java is the ability of the compiler to automatically deduce the datatype of a variable or an expression
+ with type inference, you don't have to explicitly write them.
+ Type inference helps in reducing code redundancy and improving readability.

```java
// 1. Type Inference in Generics
List<String> list = new ArrayList<>();              // Compiler infers <String> for ArrayList from the variable declaration

// 2. Local Variable Type Inference (java 10+)
var number = 10;      // int                      //var cannot be used for method parameters, fields, or return types.
var name = "Hello";   // String

// 3. Method Return Type Inference in Generics
public static <T> List<T> createList() {
    return new ArrayList<>();
}
List<Integer> numbers = createList(); // T inferred as Integer
List<String> names = createList();    // T inferred as String
```

####  type erasure

+ Type erasure is a fundamental concept in Java generics
+ Type erasure is the process of removing type information from a generic type and is not available at runtime

```java
// 1. At Compile Time (Source Code)
List<String> stringList = new ArrayList<>();
stringList.add("Hello");
String value = stringList.get(0);

// 2. After Type Erasure (Bytecode)
List stringList = new ArrayList();  // Raw type
stringList.add("Hello");           // Object type
String value = (String) stringList.get(0);  // Inserted cast
```

#### 0. Type Parameters in Generics

+ used for Custom generics

| Letter | Meaning                     |
| ------ | --------------------------- |
| `T`    | Type (general)              |
| `E`    | Element (lists/collections) |
| `K`    | Key                         |
| `V`    | Value                       |
| `N`    | Number                      |


#### 1. Generic Classes

```java
class Box<T> {                 // Custom generic
    T value;
    void set(T value) { this.value = value; }
    T get() { return value; }
}

Box<Integer> b = new Box<>();    // T inferred as Integer (T becomes Integer)
b.set(10);
```

#### 2. Generic Methods

```java
public <T> void print(T item) {
    System.out.println(item);
}

print("Hello");
print(123);
print(true);
```

#### 3. Generic Arrays

+ Direct Generic Array Creation is Illegal Due to type erasure
+ at runtime, generic type information is erased, so the JVM doesn't know what concrete type to use for the array
+ Using Collections Instead of Arrays

```java
// T[] array = new T[10];                    // Cannot create generic array
// List<String>[] listArray = new List<String>[5];  // Cannot create generic array
// E[] elements = new E[20];                 // Cannot create generic array
```

#### 4. Generic Interfaces

```jav
interface Pair<K, V> {
    K getKey();
    V getValue();
}
```

#### 5. Bounded Generics

```java
// 1. Upper Bounded
<T extends Number>         // Means T can be Integer, Double, Float

// 2. Lower Bounded
<? super Integer>           // using wildcards
```



### Collections

+ Java Collections Framework (JCF) is a set of interfaces, classes, and algorithms
+ collections are for storing and manipulating groups of objects
+ `Iterable` is the root interface for all collection classes
+ iterables can be looped using the enhanced for-loop.

```java
public interface Iterable<T> {     // Iterable Interface (Actual Code)
    Iterator<T> iterator();       // It contains only one method   
}                                   // iterator object is returned which has methods:  1. next()  2. hasNext()
// The actual object used to loop iteration is iterator
```

```text
Iterable
 └── Collection
      ├── List          Ordered, Allows Duplicates          ArrayList, LinkedList, Vector, Stack
      ├── Set            Sorted, No Duplicates             HashSet, LinkedHashSet, TreeSet
      └── Queue          Unordered, No Duplicates          LinkedList, PriorityQueue, ArrayDeque
Maps are outside Collection hierarchy but part of the framework.
└── NavigableMap, SortedMap, HashMap, LinkedHashMap, TreeMap, ConcurrentHashMap, Hashtable
```

List        → ordered, duplicates
Set         → unique elements
Queue       → FIFO
Map         → key-value
Hash        → fast
Tree        → sorted
Linked      → preserve order
ArrayList   → for reading
LinkedList  → for frequent insert/delete


| Interface | Implementation | Ordered | Sorted | Allows Duplicates | Description       |
| --------- | -------------- | ------- | ------ | ----------------- | ----------------- |
| List      | ArrayList      | ✔       | ❌      | ✔                 | Fast random access, slow insert/delete (uses dynamic array)
| List      | LinkedList     | ✔       | ❌      | ✔                 | Fast insert/delete, slow access (uses nodes)
| List      | Vector         | ✔       | ❌      | ✔                 | Synchronized version of ArrayList (rare)
| List      | Stack          | ❌       | ❌      | ✔                 | Old LIFO stack (use Deque instead).
| Set       | HashSet        | ❌       | ❌      | ❌                 | Fast, no order.
| Set       | LinkedHashSet  | ✔       | ❌      | ❌                 | Keeps insertion order.
| Set       | TreeSet        | ✔       | ✔      | ❌                 | Sorted set (uses Red-Black Tree)
| Queue     | LinkedList     | ✔       | ✔      | ✔                 | 
| Queue     | PriorityQueue  | ❌       | ✔      | ✔                 | Elements sorted by priority.
| Queue     | ArrayDeque    | ✔       | ✔      | ✔                 | Best for stacks and queues (fast).
| Map       | HashMap        | ❌       | ❌      | (Keys) ❌          | Fast, no order.
| Map       | LinkedHashMap  | ✔       | ❌      | (Keys) ❌          | Keeps insertion order.
| Map       | TreeMap        | ✔       | ✔      | (Keys) ❌          | Sorted by key.
| Map       | ConcurrentHashMap | ✔       | ✔      | (Keys) ✔          | For thread-safe high-performance maps.
| Map       | Hashtable      | ✔       | ✔      | (Keys) ✔          | Old + synchronized (avoid).


| Need                 | Use                              |
| -------------------- | -------------------------------- |
| Fast search          | **HashMap**, **HashSet**         |
| Keep order added     | **ArrayList**, **LinkedHashSet** |
| Automatically sorted | **TreeMap**, **TreeSet**         |
| Many insert/delete   | **LinkedList**                   |
| Thread-safe map      | **ConcurrentHashMap**            |
| Best stack/queue     | **ArrayDeque**                   |


| Structure                 | Access | Insert   | Delete   | Search   |
| ------------------------- | ------ | -------- | -------- | -------- |
| **ArrayList**             | O(1)   | O(n)     | O(n)     | O(n)     |
| **LinkedList**            | O(n)   | O(1)     | O(1)     | O(n)     |
| **HashSet** / **HashMap** | —      | O(1)     | O(1)     | O(1)     |
| **TreeSet** / **TreeMap** | —      | O(log n) | O(log n) | O(log n) |

#### List

+ List is a Java interface used to store ordered elements.
    - Allows duplicate elements
    - Elements are indexed (0, 1, 2, …)
    - Most used: ArrayList and LinkedList

1. ArrayList
    + Backed by a dynamic array
    + Fast access (O(1))
    + Slower insert/delete in middle (O(n))

1. LinkedList
    + Backed by a double-linked list
    + Fast insert/delete (O(1))
    + Slow access (O(n))
    + Good for queues, stacks
    + queue implementation vs list implementation
        - have different methods
        - list insert anywhere, but queue insert at back
        - list remove anywhere, but queue remove at front
        - purpose: Indexed container vs FIFO processing

3. Vector (old)
    + Synchronized version of ArrayList
    + Slow
    + Avoid unless you need legacy compatibility

4. Stack (very old)
    + Extends Vector
    + Implements LIFO
    + Better replaced by ArrayDeque



| Task                         | Best List                                                  |
| ---------------------------- | ---------------------------------------------------------- |
| Fast read by index           | **ArrayList**                                              |
| Many insert/delete in middle | **LinkedList**                                             |
| Need thread-safe list        | `Collections.synchronizedList()` or `CopyOnWriteArrayList` |
| Stack implementation         | `ArrayDeque` (better than Stack)                           |


```java
List<String> list = new ArrayList<>();
List<String> list = new LinkedList<>();
List<String> list = new Vector<>();
List<String> list = new Stack<>();

list.add("hello");
list.add(1, "X");
String value = list.get(0);
list.set(1, "NEW");
list.remove(0); // remove at index
list.remove("A"); // remove first occurrence
int size = list.size();
list.contains("A"); // true/false

for (String s : list) {}
for (int i = 0; i < list.size(); i++) {}
list.forEach(s -> System.out.println(s));          // java 8+ for-each
```

#### Set

+ Set is a Java interface used to store unique elements.
    + No guaranteed order (depends on implementation)
    + Elements cannot be duplicated
    + At most one null value (in HashSet and LinkedHashSet)
    + Most used: HashSet

1. HashSet (MOST USED)
    + Fastest set (O(1) operations)
    + No order guaranteed
    + Allows one null

2. LinkedHashSet
    + Keeps insertion order
    + Slightly slower than HashSet
    + Allows null

3. TreeSet
    + Sorted set (elements sorted in natural order)
    + No null allowed
    + Slower (O(log n))
    + Uses Red-Black Tree

| Requirement              | Best Type                                                  |
| ------------------------ | ---------------------------------------------------------- |
| Fastest performance      | **HashSet**                                                |
| Preserve insertion order | **LinkedHashSet**                                          |
| Auto-sorted elements     | **TreeSet**                                                |
| Thread-safe              | `Collections.synchronizedSet()` or `ConcurrentSkipListSet` |


```java
Set<String> set = new HashSet<>();
Set<String> set = new LinkedHashSet<>();
Set<String> set = new TreeSet<>();

set.add("A");    // adding element
set.add("A"); // ignored if already present
set.contains("A"); // true/false Check if exists
set.remove("B");
set.size();
set.clear();

// ITERATION
for (String s : set) {
    System.out.println(s);
}
set.forEach(System.out::println);
```

#### Queue

+ Queue is a Java interface used to store ordered elements (FIFO)
    - Throws exception if fails:        add(), remove(), element()
    - Returns special value if fails:   peek(), poll(), offer()
    - Best general queue = ArrayDeque

1. LinkedList (implements Queue)
    + Can be used as a queue
    + Can also be used as a stack
    + Slower than ArrayDeque but simple

2. PriorityQueue
    + Elements removed based on priority (sorted order)
    + NOT FIFO
    + Does NOT allow null
    + Great for scheduling tasks

1. ArrayDeque (BEST queue implementation)
    + Deque (Double-Ended Queue)
    + Fastest queue
    + Can be used for both queue(FIFO) and stack(LIFO)
    + No capacity limit
    + No null allowed

| Requirement                      | Best Queue                                  |
| -------------------------------- | ------------------------------------------- |
| Fast & general-purpose           | **ArrayDeque**                              |
| FIFO, simple                     | **LinkedList**                              |
| Sorted processing                | **PriorityQueue**                           |
| Thread-safe                      | `ConcurrentLinkedQueue`                     |
| Blocking (waits when empty/full) | `ArrayBlockingQueue`, `LinkedBlockingQueue` |



```java
Queue<String> q = new ArrayDeque<>();

q.offer("A");
q.offer("B");
q.offer("C");

System.out.println(q.poll()); // A
System.out.println(q.poll()); // B
System.out.println(q.peek()); // C (not removed)

for (String item : q) {
    System.out.println(item);
}
```


#### Map

+ Map is a Java interface used to store key–value pairs.
    + Each key is unique
    + Each key maps to exactly one value
    + Values can be duplicated
    + Keys cannot be duplicated
    + Map provides fast lookup, insertion, and deletion
    + Think of it like a dictionary
    + Map is NOT part of the Collection hierarchy, since a map stores key→value pairs, not single elements.
    + most used map is HashMap

1. HashMap
    a. Fastest lookup (O(1))
    b. No ordering
    c. Allows null keys and values

1. LinkedHashMap
    Keeps insertion order
    Slightly slower than HashMap
    Allows null

1. TreeMap
    a. Sorted map (keys are sorted)
    No null keys
    Uses Red-Black Tree (O(log n))

4. Hashtable (Old, synchronized)
    Thread-safe but slow
    No null allowed
    Avoid using it; use ConcurrentHashMap instead.

1. ConcurrentHashMap (For multithreading)
    Very fast
    Thread-safe without locking entire map
    No null

| Need                 | Best Map              |
| -------------------- | --------------------- |
| Fastest access       | **HashMap**           |
| Keep insertion order | **LinkedHashMap**     |
| Sorted keys          | **TreeMap**           |
| Thread-safe & fast   | **ConcurrentHashMap** |
| Old, sync            | **Hashtable** (avoid) |


```java
Map<String, Integer> map = new HashMap<>();          // Create a Map
Map<String, Integer> map = new LinkedHashMap<>();
Map<String, Integer> map = new TreeMap<>();

map.put("age", 95);                              // Add elements
int age = map.get("age");                          // Get value
map.remove("age");                              // Remove element
map.put("age", 21);                               // Update value
map.containsKey("age");                             // Check if key exists
map.containsValue(21);                              // Check if value exists
map.keySet();                                     // Get all keys
map.values();                                     // Get all values
map.entrySet();                                  // Get all key-value pairs

// Loop through all keys, or values
for (String key : map.keySet()) {             // for (String key : map.values())
    System.out.println(key);                   // for (Integer value : map.entrySet())
}
map.forEach((k, v) -> System.out.println(k + " -> " + v));      // for each
```

### Date


### Regex


### File


### Lambda Expressions

### Threads

### Stream API


### Optional Class

























