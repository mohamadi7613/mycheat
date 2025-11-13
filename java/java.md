
# Java

+ java  developed by Sun Microsystems (now owned by Oracle Corporation)
+ java follows the principle of "Write Once, Run Anywhere" (WORA)
+ Java code compiles to bytecode, not machine code. This bytecode runs on Java Virtual Machine (JVM)
+ Syntax similar to C/C++ but without complex features like pointers
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

## Java Architecture
+ download java from oracle
+ java stands by "WORA": Write Once, Run Anywhere

1.  JDK = Java Development Kit
    JDK = JRE + Development Tools (javac, debugger, documentation tools, etc.)
    JDK = as a developer you install JDK, but for running the java program you just need JRE.
 
3.  JRE = Java Runtime Environment
    JRE = JVM + Java Class Libraries  
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


## Basic
+ our code without "signature" not works, it gives error
+ jvm looks for this particluar signature to run the code
+ name of the file should be same as the name of the class
+ is better to use CamelCase for class names

```java
// signature: 1. public 2. class 3. Main                       // 1. public 2. static 3. void 4. main 
// public static void main(String[] a) is Entry point of every Java program                                      
//  Main.java                                           // public means this class is accessible from other classes
public class Main {                                  // 'Main' is the name of our class (should match the filename: Main.java)
    public static void main(String[] args) {         // 'static': belongs to the class rather than any object instance
        System.out.println("Hello, World!");        // 'void': doesn't return any value
    }                                              // The main method is the entry point 
}                                                 // 'String[] args': parameter that accepts command-line arguments
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
-----------------------------------------
| float   | 4 bytes | float f = 1.23f;  |
| double  | 8 bytes | double d = 1.23;  |
-----------------------------------------
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



### operators
1. arithmetic operators               + - * / % ++ --   
    - addition, subtraction, multiplication, division, modulus(remainder)
    - increment and decrement operators  ++ --
2. relational operators               > < >= <= == !=
3. logical operators                  && || !
    a. truth table 
4. bitwise operators                  & | ^ ~ << >>
5. assignment operators               = += -= *= /= %= &= |= ^= <<= >>= >>>=
6. Unary operators (One-operand operations)     ++ -- + - !
7. ternary operator                  ? :

#### arithmetic operators

+ operands = a, b
+ operators = + - * / %
+ expersion = a + b
+ statement = int num = 10;

```java
int num = 10;
num += 10;
num -= 10;
num *= 10;
num /= 10;
num %= 10;
num &= 10;
int p = num++;                   // post increament operator     // num is 11, p is 10  // first fetch the value then increament
num--;                   // post decreament operator    // we can write in one statement
int p = ++num;                     // pre increament operator    // num is 11, p is 11  // first increament then fetch
```

### parantesis

+ Grouping expressions

```java
int i = 10; 
System.out.print("" + i + 5);     // 105
System.out.print("" + (i+5));    // 15
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

### Bitwise operators

```java

```

### Unary Operators

+ Operate on a single operand.

```java
int a = 10;
+a;         // +10   // Unary plus
-a;         // -10   // Unary minus -> negates value
++a;         // 11    // Pre-increment
a++;          // 11   // Post-increment
!a;           // Error  // Logical NOT
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
for (int n : nums) {
    System.out.println(n);
}
```

### Loop Control Statements

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






















