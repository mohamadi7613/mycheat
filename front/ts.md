

# TypeScript

+ TypeScript is a superset of JavaScript
+ TS does not make our code faster
+ TS adds static type checking
+ compiler can catch errors at compile time, before your code runs
+ we can’t console.log types of variables (ts types) directly at runtime (TypeScript types exist only at compile time)
+ How It Works
    - You write code in .ts files.
    - TS compiles your code into plain JavaScript (es5).
    - That JavaScript runs in browser or Node.js.


# commands
```bash
npm install typesctipt --save-dev       # install as dev dependency
tsc -v                                   # version
tsc --init                              # create a recommended tsconfig
tsc 1.ts                      # compile typesctipt file to es5 
tsc 1.ts --watch              # watch mode
ts-node | nodemon --watch               # install ts-node to run .ts files
```

# TsConfig
```json
{   // important settings
    "compilerOptions": {    //
        "target": "es5",   // compile to es5
        "experimentalDecorators": true,   // for using decorators
        "emitDecoratorMetadata": true,    // for using decorators
        "module": "commonjs",
        "outDir": "./dist",
        "rootDir": "./src",
        "allowJs": true,                     // allow js
        "noEmitOnError": true,               // don't emit if we have even one error in ts
        "removeComments": true,               // remove comments
    }
}
```

## Type Checking

+ js is `run-time type checking` so we can errors when the app is running
+ ts is `static type checking` so we can see errors in the vscode 

```js
function sum(a,b,c){return a+b+c}
sum(a,b)   // you can see error in your editor

let age: number = 25;
age = 'hello'   // you can see error in your editor
```

### Basic Types

+ hover in your editor to see the types
+ the syntax is called "type annotation"

```js
let isDone: boolean = false;
let age: number = 25;
let name: string = "John";

let hobbies: string[] = ['Programming', 'Cooking'];  // Array of strings
let a: number[2] = [1, 2];                            // number[2] is not valid TypeScript syntax (error)
let t: [number, string] = [1, 'hello'];               // tuple of number and string (typed array)

let symbolValue: symbol = Symbol("id");               // symbol type     
let user: { name: string; age: number } = { name: "John", age: 30 };   // object type 

// 1. void, 2. null, 3. undefined, 4. unknown, 5. any, 6. never
let myCar: any = 'BMW';                                 // try to not use any 
let unknownVar: unknown = "maybe a string";             // safer than `any`
let unusable: void = undefined;                         // valid values for void type are undefined and nothing
let u: undefined = undefined;                            // explicitly set to undefined
let n: null = null;                                     // initially set to null
```

### any type
+ Avoid any when possible - it defeats TypeScript's purpose
+ Enable noImplicitAny in tsconfig to catch unintended any usage

```js
let a: any = 45;        // Compatible with all types
a= "string"             // no error
a= []                   // no error
a.get()                 // no error but runtime error

let dynamicArray: any[] = [1, "two", true];                             // dynamic type with any
const apiResponse: any = await fetch('https://api.example.com/data');        // usefull for dynamic content
let userInput: any = getUnknownInput();                                      // usefull When you don't know the type yet
let a:any = lib()                                                       // useful for user or a 3rd party library

// alternative for any
function getUser(id: any): any {}                              // avoid this
let u: unknown = getData();                                    // Safer than any
let flexibleArray: (string | number)[] = [1, "hello"];          // Union types
```


### Unknown type
```js
let a: unknown;                              // any vs unknown 
let b: any = 45;                             // dot notation
a.toLowerCase();                             // error unlike any 
b.toLowerCase();                             // no error


function processValue(value: unknown) {         // unknown requires type checking
  if (typeof value === "string") {              // Unlike any, unkown requires you to perform type checks
    console.log(value.toUpperCase());           // Safe: 'value' is now a string (it's possible now to use dot notation)
  } else if (typeof value === "number") {
    console.log(value + 10);                    // Safe: 'value' is now a number
  } else {
    console.log("Unknown type");
  }
}
```

### Never type

+ use never for error
+ it is a way of ensuring that certain code paths are truly unreachable
+ No values can be assigned to "never" (except other "never" values)
+ Functions returning "never" can't have a reachable end point
+ useful for functions that:
    - never return anything
    - always throws an error
    - infinite loops
    - A variable that can never have a value
    - empty arrays
    - impossible states in switch-case

```js
// 1. functions
function throwError(message: string): never {     // type never for return value of function
  throw new Error(message);                       // never return anything but throws an error
}
function infiniteLoop(): never {                 // type never
    while (true) {}
}
throwError("Something went wrong!");
console.log("this line will not run")  // editor show error [config: errorOnUnreachableCode: false]

// 2. array
const emptyArray: never[] = [];             // type never for empty array

// 3. switch-case
type Success = { type: "success"; data: string };
type Error = { type: "error"; message: string };

type Result = Success | Error;

function handleResult(result: Result) {
    switch (result.type) {
        case "success":
            console.log(result.data);
            break;
        case "error":
            console.error(result.message);
            break;
        default:
            // TypeScript knows this should never happen
            const unexpected: never = result;
            throw new Error(`Unexpected result: ${unexpected}`);
    }
}
```

#### Void type

+ void is a type that represents the absence of a value
+ indicate that a function doesn't return a meaningful value
+ Equivalent to undefined in most contexts

```js
// 1. function
function logMessage(message: string): void {     // void type
    console.log(message);                      // No return statement
}
// 2. arrow function
const printError = (error: string): void => {console.error(error);};     // arrow function with void type
// 3. listener function
buttons.forEach((button): void => {
    button.addEventListener('click', () => {
        console.log('Button clicked');
    });
});
// 4. relatioin with undefined
function implicitVoid(): void {
    return undefined; // Allowed
    return;          // Allowed
}
```

#### undefined type

+ The undefined type in TypeScript represents a variable that has been declared but hasn't been assigned a value
+ or explicitly set to undefined
+ void vs undefined:

```ts
// Returns undefined explicitly
function returnsUndefined(): undefined {
    return undefined;                // Must have return statement
}

// Returns void (no return value)
function returnsVoid(): void {
    console.log("No return");         // No return statement needed
}

// union type
let age: number | undefined;                          // union type with undefined

// real example
function processValue(value: string | undefined): void {
    if (value === undefined) {                       // value is string | undefined
        console.log("Value is undefined");
        return;
    }
    console.log(value.toUpperCase());     // Safe // TypeScript knows value is string here
}
```

### null type

+ The null type represents the intentional absence of a value
+ It's used to explicitly indicate that a variable should have no value

```ts
let emptyValue: null = null;
let maybeNull: string | null = null;

console.log(emptyValue); // null
console.log(maybeNull); // null

// null vs undefined
let n: null = null;                    // - null: "value is intentionally empty"
let u: undefined = undefined;          // - undefined: "value is not assigned"

console.log(n == u);  // true (loose equality)
console.log(n === u); // false (strict equality)
```


### Array

```ts
let numbers: number[] = [1, 2, 3, 4];                       // type[]
let names: Array<string> = ["Ali", "Sara"];                 // <Generic> Syntax //Array<type>
let matrix: number[][] = [[1,2,3],[4,5,6]]                            // multi-dimensional matrix
const colors: readonly string[] = ["red", "green", "blue"];   // colors.push("a") error  //readonly is just for arrays
let dynamicArray: any[] = [1, "two", true];                 // dynamic type with any
let person: {}[]                                            // array of objects
let a: [string, ...number[]] = ["a", 1, 2, 3]                     // ['a'], ['a',1], ['a',1,2]   // String + multiple numbers
let a: [string, ...number] = ["a", 1, 2, 3]                     // Error  // rest operator should be an array
let list: string[] = [1,2,3].map((id : number): string=> `${id}`)  // es6 method: return-type and argument-type
```

#### Tuples

+ A tuple in TS is a special array type that knows:
    - exactly how many elements it contains
    - the specific type of each element at fixed positions.
    - order matters

```js
let address: [string, number] = ["Street", 99];              // an array of fiexd length with diff types included
let user: [string, number, boolean] = ["John", 25, true];    // a tuple type
user[0] = 16                                                // Error
address.push("Street 2");                                   // this is possible for tuples
let a: number[2] = [1, 2];                                  // Error // number[2] is not valid TypeScript syntax (error)
let b:[number, number, number]=[1,2,3,4]                    // Error  // a tuple with exactly 3 elements
const c: readonly [number, number] = [1, 2, 3];             // we cannot push anymore   // readonly tuple
```

### Length checking for arrays

+ there is no way to assign a number for length of an array, we should do it manually
+ if we have 20 number and 20 string for an array, it will be very hard to do it

```js

```


#### Unions

```js
let value: string | number;     // combined types (or meaning)
value = "Hello"; // Valid
value = 42;      // Valid
value = true;    // Error: Type 'boolean' is not assignable to type 'string | number'.
let arr: (string | number)[] = ["a", 1, "b", 2];   // array of string or number  // array of union: order does not matter
let a:[string | number] = [1]                     // array of string or number with 1 element  // [1, 's'] is not valid
```


#### Enums
```js
// behind the scene enums are objects in es5 
enum Color { //set of datatypes with numeric values
    Gray, // 0 // by default starts by zero
    Red, // 1
    Green = 100, // 100 // we can change the value
    Blue, // 101 // the next is based on previouse
    Yellow = 2 // 2
    Pink = "Pink"  // returns the string but if you set a string for one, you need to specify string for all the memebrs too
} // like days of week
const myColor: Color = Color.Green
console.log(myColor); // Prints: 100
```

#### Rest operator

+ rest operator in js:
    1. Rest Parameters in Functions
    2. Array Destructuring with Rest
    3. Object Destructuring with Rest

```ts
// 1. Rest Parameters in Functions
function sum(...numbers: number[]): number {
    return numbers.reduce((total, num) => total + num, 0);
}
console.log(sum(1, 2, 3)); // 6

// 2. Rest operator With Regular Parameters
function greet(greeting: string, ...names: string[]): string {
    return `${greeting} ${names.join(', ')}!`;
}
console.log(greet("Hello", "John", "Jane")); // "Hello John, Jane!"

// 3. Array Destructuring with Rest
const colors = ["red", "green", "blue", "yellow", "purple"];
const [first, second, ...remaining] = colors;
console.log(first); // "red"
console.log(second); // "green" 
console.log(remaining); // ["blue", "yellow", "purple"]
```

####### Rest operator in TS only

+ The rest operator in TS represents an indefinite number of arguments as an array
+ Tuple Rest Patterns:

```ts
// TS only - tuple types with rest
type StringNumberBooleans = [string, number, ...boolean[]];
type StringBooleansNumber = [string, ...boolean[], number];

const a: StringNumberBooleans = ["hello", 1, true, false];
const b: StringBooleansNumber = ["hello", true, false, 42];

let a: [string, ...number[]] = ["a", 1, 2, 3]                     // ['a'], ['a',1], ['a',1,2]   // String + multiple numbers
let a: [string, ...number] = ["a", 1, 2, 3]                     // Error  // rest operator in tuples should be an array
```


### Type Aliases

+ Callable Types = Type Aliases + Interfaces
+ Type Aliases allow you to create a new name for any type.
+ They're used to:
    - create custom types
    - combine existing types
    - give meaningful names to complex types.

```ts
// 1. Meaningful names
type ID = number;
type Name = string;
type IsActive = boolean;

const userId: ID = 123;
const userName: Name = "John";
const userActive: IsActive = true;

// 2. Combining types
type StringOrNumber = string | number;
type MaybeString = string | null | undefined;
type Truthy = true | 1 | "true" | "yes";

let value: StringOrNumber = "hello";
value = 42;         //  Allowed

// 3. Object type alias
type User = {               // its better to use interface instead of type
    id: number;
    name: string;
    email?: string;
    readonly createdAt: Date;
};

const user: User = {
    id: 1,
    name: "John",
    createdAt: new Date()
};
```

#### Literal Types

+ Literal Types allow you to specify exact values that a variable can have, rather than just general types.
+ specific allowed values

```js
type Direction = "left" | "right" | "up" | "down";   // string literal type
type DiceValue = 1 | 2 | 3 | 4 | 5 | 6;                // number literal type

let move: Direction = "left";
move = "right";     //  Allowed
move = "diagonal";  //  Error
```

### Objects
```js
/////// Object type
let a: object = { name: 'Max', age: 27 };
let a: {} = { name: 'Max', age: 27 };
/////// Inline type
let userData: { name: string, age: number } = { 
    name: 'Max',
    age: 27
};
userData = {
    name: 'Robert',
    age: 'string'   // Error
};
///////// custome type  (type alias)
type Person = {  // declare custome type // Pascallcase
  name: string; 
  age: number;
  year?: number; // Optional property
};
const person: Person = {
  name: "Alice",
  age: 30,
};
```

### Interface

+ Interfaces are similar to custom types but often preferred for defining object structures.

```ts
interface Person {                 /////// interface
  name: string;
  age?: number;            // optioanl property
  country: "USA" | "UK" | "Germany",  // Union type inside the interface
  readonly width: number;   // Readonly property
  get: (name: string) => void;   // Method that gets name and returns void
}
const person: Person = {
  name: "Alice",
  age: 30,
  width: 100
};
perspn.width = 200; // Error
```

### Type vs Interface
```js
// we can have multiple interfaces with the same name but we cannot redclare types
interface Person {
  name: string;
  age: number;
}
interface Person {
  country: string;  // include this property
}
const person: Person = {
  name: "Alice",
  age: 30,
  country: "USA"
};
/////// interfaces use extends for inheritance but types use &
interface vehicle {
    name: string;
    color: string;
}
type car = vehicle & {speed: number};  // we can combine types and interfaces with &
```

#### Type inference 

+ Type inference: automatic detection of the type by compiler without explicit type annotations
+ Type Assertion: set the type of a value by developer ("as" syntax)

```js
// 1. Type inference
let a = 5                  // This variable will automatically be assigned a number type.
a ="hello"                  // Error bcz of type inference
// TypeScript infers the type from the initial value ( Equivalent to explicit typing:)
let name = "John";        // inferred as string        // equivalent to: let name: string = "John";
let age = 25;             // inferred as number         // equivalent to: let age: number = 25;
let isActive = true;                                        // inferred as boolean
let scores = [85, 92, 78];                                  // inferred as number[]
let numbers = [1, 3.22, 6, -1]                              // inferred as number[]
const mixed = [1, "hello", true];                           // inferred as (string | number | boolean)[]
let obj = { name: "John", age: 25 };                            // inferred as { name: string, age: number }
const empty = [];                                           // inferred as any[] (be careful!)
const mixedArray = [1, "hello", true, { name: "John" }];     // inferred as: (number | string | boolean | { name: string; })[]
function sum(a: number, b: number) {return a + b}           // the return-type will be number by Type inference
function processUser(user) {                               // Parameters are not inferred - use explicit types
    return user.name.toUpperCase();                       // user is 'any'
}
```

### Type Assertion

+ Type Assertion: set the type of a value by developer ("as" syntax)
+ developer tells the compiler to treat a value as a specific type
+ overriding TypeScript's default `type inference`
+  It's like saying "trust me, I know what I'm doing" to the TS compiler. (I know better than compiler)

```ts
// 1. as Syntax (Recommended)
let a: any = "hello world";
let length: number = (a as string).length;

// 2. Angle-Bracket Syntax
let a: any = "hello world";
let length: number = (<string>a).length;

// 3. Common Use Cases: Working with DOM Elements
let input = document.getElementById("input") as HTMLInputElement;   // getElementById returns a generic HTMLElement | null type.

// 4. Comon Use Cases:         API Responses and JSON Parsing
async function fetchUser() {                    // Fetch API returns any
    const response = await fetch("/api/user");
    const user = await response.json() as User;
    // Now treated as User type
    return user.name; //  TypeScript knows this is string
}

interface User {
    id: number;
    name: string;
    email: string;
}
```

### Non-Null Assertion

```js
function getLength(value: string | null) {
  return value!.length; // Asserts that 'value' is not null
}
console.log(getLength("hello")); // Works
console.log(getLength(null));    // Runtime error
////
document.getElementById("foo")!.innerHTML = "Hello";  // getElementById can return null if it doesn't exist 
```


### Type Narrowing

+ Narrowing is when a variable is narrowed down to a specific type
+ Type Narrowing is the process of refining a variable from a broader type to a more specific type.
+ TypeScript uses control flow analysis to narrow types based on checks and guards in your code.
+ refining a general type
+ allowing the compiler to provide better suggestions
+ there are some techniques to narrow types

```ts
// 1. typeof Guards
function processValue(value: string | number) {
    if (typeof value === "string") {                            // Narrowed to string
        console.log("It's a string:", value.toUpperCase());      // TypeScript knows value is string here
    } else {                                                // Narrowed to number
        console.log("It's a number:", value.toFixed(2));  // TypeScript knows value is number here
    }
}
// 2. Truthiness/Falsiness Checks
function printLength(str: string | null | undefined) {
    if (str) {
        console.log(str.length);                      // str is string (not null or undefined)
    } else {
        console.log("No string provided");           // str is null or undefined
    }
}
////// 3. instanceof
class Dog {
  bark() {
    console.log("Woof!");
  }
}
class Cat {
  meow() {
    console.log("Meow!");
  }
}
function interact(pet: Dog | Cat) {
  if (pet instanceof Dog) {
    pet.bark(); // Narrowed to Dog
  } else {
    pet.meow(); // Narrowed to Cat
  }
}
/////// 4. in
type Shape = { radius: number } | { sideLength: number };
function getShapeInfo(shape: Shape) {
  if ("radius" in shape) { //check existence of a property in an object
    console.log("Circle with radius:", shape.radius); // Narrowed to { radius: number }
  } else {
    console.log("Square with side length:", shape.sideLength); // Narrowed to { sideLength: number }
  }
}
/////// 5. is
type Dog = { kind: "dog"; bark: () => void };
type Cat = { kind: "cat"; meow: () => void };
function isDog(animal: Dog | Cat): animal is Dog {    // like boolean
  return animal.kind === "dog";
}
function interact(animal: Dog | Cat) {
  if (isDog(animal)) {
    animal.bark(); // Narrowed to Dog
  } else {
    animal.meow(); // Narrowed to Cat
  }
}
```


### Functions

+ A function signature defines the contract of a function
+ A function signature consists of:
    - Function name
    - Parameter types and names
    - Return type

```js
function add(a: number, b: number): number {            // Signature: (name: string) => string  
  return a + b;                                           // type for 1. parameters 2. return type
}
const multiply = (a: number, b: number = 2): number => a * b;      // es6 syntax

function sayHello(): void {  // void means nothing is returned
    console.log('Hello!');
    // return false; // error
}

function multiply(a: number, b: number =50, c?: string): string { // c is optional
    const result = c?.toLowerCase();   // to prevent error when c is undefined
    return a*b; // we get error since we specify return type as string
}
function sum(...numbers: number[]): number {  //Rest Parameters
    return numbers.reduce((a, b) => a + b, 0); // sum(2,3,2,3)
}
function throwError(message: string): never { // never means that the function never returns anything
    throw new Error(message);  // for x they throw errors
}
function callIt(fn: () => void) {  // ()=> void means is a type and it returns nothing
    console.log("Calling it...");  // ()=> boolean means is a type and it returns a boolean
    fn();
}
function callIt(fb: Function) { // Function is a type
    console.log("Calling it...");
    fb();
}
```


##### Function Overloading

+ Function Overloading allows you to define multiple function signatures for the same function
+ call a function with different parameter types and return types

```js
// 1. Overload signatures
function combine(a: string, b: string): string;  
function combine(a: number, b: number): number;

// 2. Implementation signature (not visible from outside)
function combine(a: any, b: any): any {
    return a + b;
}

// 3. usage
const result1 = combine("Hello, ", "World!"); // string
const result2 = combine(10, 20);             // number


//  ----- common usecase for function overloading  ---  //

// 1. Overloads for API requests
function fetchData(endpoint: "users"): Promise<User[]>;
function fetchData(endpoint: "posts", userId: number): Promise<Post[]>;
function fetchData(endpoint: "comments", postId: number, limit?: number): Promise<Comment[]>;

// 2. Implementation
async function fetchData(
    endpoint: string, 
    id?: number, 
    limit?: number
): Promise<any> {
    let url = `https://api.example.com/${endpoint}`;
    
    if (id) {
        url += `/${id}`;
    }
    
    if (limit) {
        url += `?limit=${limit}`;
    }
    
    const response = await fetch(url);
    return response.json();
}

// 3. Usage
const users = await fetchData("users");                      // Promise<User[]>
const posts = await fetchData("posts", 123);                // Promise<Post[]>
const comments = await fetchData("comments", 456, 10);      // Promise<Comment[]>
```

#### Type Aliases for Function
```js
type GreetFunction = (name: string) => string;  //define the shape of a function
const greet: GreetFunction = (name) => `Hello, ${name}!`;
```

#### Higher-Order Functions
```js
function applyOperation(x: number, y: number, operation: (a: number, b: number) => number): number {
    return operation(x, y);
}
const sum = applyOperation(5, 3, (a, b) => a + b); // 8
```


### Declare

+ declare is used to tell the compiler that a variable, function, class, or module exists elsewhere (in JavaScript code or the environment) 
+ something exists without providing an actual implementation. 
+ It's used for ambient declarations.

```js
// 1. Declare global variables
declare const APP_VERSION: string;           // exist at runtime from elsewhere
declare const IS_PRODUCTION: boolean;

// 2. Declare global functions                          // functions that exist in global scope
declare const ghost: { boo: () => void };       // function exists even if not cannot be found  in the current code
declare function showNotification(message: string, type?: "info" | "error"): void;
showNotification("Hello World!", "info");             // Usage
```


#### Index Signatures
```js
interface Dictionary {   // define the shape of an field
  [key: string]: string;  // when we do not know about the property name
} 
const translations: Dictionary = {
  hello: "Hola",
  world: "Mundo",
};
```

#### Intersection Types
```js
type Movable = {
  x: number;
  y: number;
};
type Scalable = {
  scale: number;
};
type Transformable = Movable & Scalable;  // combine types
const object: Transformable = {
  x: 10,
  y: 20,
  scale: 1.5,
};
```

### Namespace

+ Namespaces in TypeScript formerly called "internal modules"
+ Namespaces organize code into logical groups and prevent naming collisions
+ They're particularly useful for organizing related functionality and creating modular code structures

```ts
namespace Utilities {

    // Private to the namespace
    const API_KEY = "secret-key-123";
    let transactionCount = 0;
    
    function generateTransactionId(): string {
        transactionCount++;
        return `TXN-${Date.now()}-${transactionCount}`;
    }
    
    // public
    export function formatDate(date: Date): string {
        return date.toISOString().split('T')[0];
    }
    
    export function capitalize(str: string): string {
        return str.charAt(0).toUpperCase() + str.slice(1).toLowerCase();
    }
    
    // Not exported - only accessible within namespace
    function helperFunction(): void {
        console.log("Helper called");
    }
}

// Usage
const today = Utilities.formatDate(new Date());
const name = Utilities.capitalize("john");
// Utilities.helperFunction(); // ❌ Error: helperFunction is not exported
```

#### Nested Namespaces

```ts

namespace MathOperations {
    export namespace Basic {
        export function add(a: number, b: number): number {
            return a + b;
        }
        
        export function subtract(a: number, b: number): number {
            return a - b;
        }
    }
    
    export namespace Advanced {
        export function power(base: number, exponent: number): number {
            return Math.pow(base, exponent);
        }
        
        export function sqrt(num: number): number {
            return Math.sqrt(num);
        }
    }
}

// Usage
const sum = MathOperations.Basic.add(5, 3);
const power = MathOperations.Advanced.power(2, 8);
```

### OOP
```js
///// 1. class
class Person {
  name: string;
  age: number;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
  greet(): string {
    return `Hello, my name is ${this.name}.`;
  }
}
///// 2. Access Modifiers
class Employee {
  private salary: number;  // private( only within class), public (default), protected(within class and subclasses)
  constructor(private name: string, salary: number) {
    this.salary = salary;
  }
  getSalary(): number {
    return this.salary;
  }
}
const emp = new Employee("Alice", 5000);
console.log(emp.salary); // Error: `salary` is private
////// 3. get and set accessors
class Example {
  private _value: number = 0; // Backing property
  get value(): number {  // getter works like an attribute not a function
    return this._value;
  }
  set value(newValue: number) {  // setter works like an attribute not a function
    if (newValue < 0) {
      throw new Error("Value must be positive");
    }
    this._value = newValue;
  }
}
const example = new Example();  // setter and getter allows us to set and get value like a variable not a function
example.value = 10;         // Sets value using the setter
console.log(example.value); // Gets value using the getter
example.value = -5;         // Throws error: Value must be positive
///// 4. Extending Objects: Inheritance
interface Shape {
  color: string;
}
interface Circle extends Shape { //inherit from Shape
  radius: number;
}
const myCircle: Circle = {
  color: "red",
  radius: 10,
};
////// 5. Implementing Interfaces
interface Person {
    name: string;  // enforce the class to have a property
}
interface CanRun { 
    run(): void; // enforce the class to have a run method
} 
interface CanJump {
    jump(): void; //  ensures that the class provides implementations
}
class Athlete implements Person, CanRun, CanJump {  // we can implement multiple interfaces
    name: string           // implements Person
    constructor(name: string) {}  // constructor to initialize instances
    run() {  // implements CanRun
        console.log("Running fast!");
    }
    jump() {
        console.log("Jumping high!");
    }
}
///// 6. Abstract Classes
abstract class Shape {  // An abstract class cannot be instantiated
    protected name: string
    constructor(name: string) {
        this.name=name
    }
    abstract area(): number; // abstract methods must be implemented by subclasses
    display() { // in abstract class we can define methods despite Interfaces
        console.log(this.name);  // native functions declare in abstract class
    }
}
class Circle extends Shape {  // Abstract classes should be extended
  constructor(private radius: number) {
    super("Circle");   // super is the constructor of the parent class
  } 
  area(): number {
    return Math.PI * this.radius ** 2;
  }
}
///// 7. Polymorphism
class Shape {
  area(): number {
    return 0; // Default behavior
  }
}
class Rectangle extends Shape {
  constructor(private width: number, private height: number) {
    super();
  }
  area(): number {
    return this.width * this.height;
  }
}
const shapes: Shape[] = [new Shape(), new Rectangle(5, 10)];
shapes.forEach((shape) => {
  console.log(shape.area());
}); // 0 (Shape) // 50 (Rectangle)
```


### Generics
```js
///// 1. generic functions
function identity<T>(value: T): T {   // write <T> or <Type> affter the function name
  return value;    // we do not know the type
}
const result1 = identity<number>(42); // Explicitly specify type
const result2 = identity("Hello");    // TypeScript infers the type
console.log(result1); // 42  // we access methods of the type in our editor
console.log(result2); // "Hello"
 
/////// 2. generic classes
class Box<T> {
  private content: T;
  constructor(content: T) {
    this.content = content;
  }
  getContent(): T {
    return this.content;
  }
}
const numberBox = new Box<number>(123);
 
/////// 3. generic interfaces
interface Pair<K, V> {
  key: K;
  value: V;
}

const pair: Pair<string, number> = { key: "age", value: 30 };

////// 4. generic default values
function createArray<T = number>(length: number, value: T): T[] {
  return Array(length).fill(value);
}
const numbers = createArray(5, 0); // T is inferred as number
const strings = createArray<string>(3, "hello"); // Explicitly specify T

/////// 5. generic constraints
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];  // set limit on the type of obj[key]
}
const person = { name: "Alice", age: 25 };
const error = getProperty(person, "height"); // Error: Argument of type '"height"' is not assignable
```

### Decorators
Decorators are just functions that modify the behavior of a class or a method.
```js
///// 1. class decorators
function AddTimestamp(constructor: Function) {  // we access the constructor of the class as a parameter 
  constructor.prototype.timestamp = new Date(); // we add a new property to the class
  constructor.prototype.injected = true;            // we add a new property to the class
}

@AddTimestamp  // this is decorator which should annotate the class
class User {}  // no need to write pranthesis for decorator

const user = new User() as any;
console.log(user.timestamp); // Outputs the current timestamp

////// 2. method decorators
function Log(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;

  descriptor.value = function (...args: any[]) {
    console.log(`Method ${propertyKey} called with args: ${args}`);
    return originalMethod.apply(this, args);
  };
}

class Calculator {
  @Log
  add(a: number, b: number) {
    return a + b;
  }
}

const calculator = new Calculator();
console.log(calculator.add(2, 3)); 
// Logs:
// Method add called with args: 2,3
// 5
/////// 3. parameter decorators
function ReadOnly(target: any, propertyKey: string) {
  Object.defineProperty(target, propertyKey, { writable: false });
}

class Person {
  @ReadOnly
  name: string;

  constructor(name: string) {
    this.name = name;
  }
}

const person = new Person("Alice");
console.log(person.name);  // Alice
// person.name = "Bob";  // Error: Cannot assign to 'name' because it is a read-only property.

```


### React + TypeScript


```bash
npx create-react-app my-app --template typescript
```

#### 1. HTMLElement
```js
let root = document.getElementById("root")
ReactDom.createRoot(root as HTMLElement).render(<App/>)
```

1. setState

```js
enum Difficulty{  // we can create  a folder 'types'
    easy,
    hard,
    medium
}
const [step,setStep] = useState<Difficultyg>(Difficulty.easy)
const [value,setValue] = useState<Number>(1)
const [user, setUser] = useState<User>({} as User);    // using type assertion
```
2. props

```js
interface Props{ // we can create a folder 'types'
    name: string,
    age: number
}
export function App(p: Props){return <>{p.name}</>}
export function App({name, age}: Props){return <>{name}</>}   // destructuring as type of interface
export function App(props:{name: string, age:number}){return <>{name}</>}   // props is argument as type of inline interface
```
##### 5. Function Component
```js
// Easiest way to declare a Function Component; return type is inferred.
const App = ({ message }: AppProps) => <div>{message}</div>;

// You can choose to annotate the return type so an error is raised if you accidentally return some other type
const App = ({ message }: AppProps): React.JSX.Element => <div>{message}</div>;
```

3. JSX

```js
const renderElements = (): JSX.Element[] => {
    let elements = []
    for(let index=0;index <4; index++){
        elements.push(<div>item</div>)
    }
    return elements;
}
```

4. axios

```js
// json-to-ts online tools
interface Person{   // json
    id: number,
    name: string
}
interface getPersonsResponse{
    persons: Person[]   // array of json
}
const {data} = await axios.get<getPersonsResponse>('url')
```

#### 5. Array methods
```js
arr.map((item: number) => item * 2)
```

#### 6. Evenet Handlers
```js
const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
    console.log(event.target);
};

return <button onClick={handleClick}>Click Me</button>;
```











