


# TypeScript
ts does not make our code faster



# commands
```bash
tsc v       # version
tsc --init  # create a recommended tsconfig
tsc ./src/index.ts     # compile typesctipt file to es5 
tsc ./src/index.ts --watch    # 
ts-node | nodemon --watch        # install ts-node to run .ts files
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
js is `run-time type checking` so we can errors when the app is running
ts is `static type checking` so we can see errors in the vscode 
```js
function sum(a,b,c){return a+b+c}
sum(a,b)   // you can see error in your editor
```

### Basic Assign Types
hover in your editor to see the types
```js
const myName: string = 'Robert';
const myAge: number = 24;
const bHasHobbies: boolean = true;
const hobbies: string[] = ['Programming', 'Cooking'];  // Array of strings
let myCar: any = 'BMW'; // useful for user or a 3rd party library
let unusable: void = undefined;   // valid values for void type are undefined and nothing
```
### Unknown
```js
let a: unknown;
a.toLowerCase();      // error unlike any 
function processValue(value: unknown) {   // a type-safe counterpart to any
  if (typeof value === "string") { //Unlike any, unkown requires you to perform type checks
    console.log(value.toUpperCase()); // Safe: 'value' is now a string
  } else if (typeof value === "number") {
    console.log(value + 10); // Safe: 'value' is now a number
  } else {
    console.log("Unknown type");
  }
}
```
### Never
```js
// useful for functions that never return or infinite loops
function throwError(message: string): never {
  throw new Error(message);
}
throwError("Something went wrong!");
console.log("this will not run")  // editor show error [config: errorOnUnreachableCode: false]
```

### Array
```js
const numbers: number[] = [1, 2, 3, 4];   // type[]
// const names: Array<string> = ["Alice", "Bob", "Charlie"]; //Array<type>
const matrix: number[][]=[[1,2,3]]   // mutulidimensional matrix
const colors: readonly string[] = ["red", "green", "blue"];   // colors.push("a") error  //readonly is just for arrays
const dynamicArray: any[] = [1, "two", true];   // dynamic type with any
const person: {}[]     // array of objects
type RestTuple = [string, ...number[]];    // rest operator should be an array
const list: string[] = [1,2,3].map((id : number): string=> return `${id}`)  // return type and argument type for array method
```
#### Tuples
```js
const address: [string, number] = ["Street", 99]; // this is  tupple. It is an array of fiexd length with diff types included
const a:[number, number, number]=[1,2,3,4]    //Error
address.push("Street 2");   // this is possible for tupples
const a: readonly [number, number] = [1, 2, 3];   // we cannot push anymore
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
#### Unions
```js
let value: string | number;     // combined types
value = "Hello"; // Valid
value = 42;      // Valid
value = true;    // Error: Type 'boolean' is not assignable to type 'string | number'.
let arr: (string | number)[] = ["a", 1, "b", 2];   // array of string or number
```
#### Literal Types
```js
type Direction = "left" | "right" | "up" | "down";   // string literal type
type DiceValue = 1 | 2 | 3 | 4 | 5 | 6;                // number literal type
```
#### Type inference vs Type Assertion
Type Assertion: set the type of a value by developer (we can use "as")
Type inference(Type Casting): automatic detection of the type by compiler
```js
const numbers = [1, 3.22, 6, -1] // This variable will automatically be assigned a number[] array type.
let a = 5                  // This variable will automatically be assigned a number type.
a ="hello"                  // Error bcz of type inference
function sum(a: number, b: number) {return a + b}   // the return type will be number by Type inference
////
let input = document.getElementById("input") as HTMLInputElement;   // type assertion and I know better than compiler   // getElementById returns a generic HTMLElement | null type.
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


### Functions

```js
function multiply(a: number, b: number =50, c?: string): string { // c is optional
    const result = c?.toLowerCase();   // to prevent error when c is undefined
    return a*b; // we get error since we specify return type as string
}
function sum(...numbers: number[]): number {  //Rest Parameters
    return numbers.reduce((a, b) => a + b, 0); // sum(2,3,2,3)
}
function sayHello(): void {  // void means nothing is returned
    console.log('Hello!');
    // return false; // error
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
```js
function combine(a: string, b: string): string;  // we cannot define it as type
function combine(a: number, b: number): number;
function combine(a: any, b: any): any {
    return a + b;
}
const result1 = combine("Hello, ", "World!"); // string
const result2 = combine(10, 20);             // number
```

#### Callable Types (Type Annotations for Functions)
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
```js
// Declare means that this variable exists event if not cannot be found  in the current code
declare const ghost: { boo: () => void };
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
/////// interface
interface Person { //// Interfaces are similar to custom types but often preferred for defining object structures.
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


### Type Narrowing
+ refining a general type
+ allowing the compiler to provide better suggestions
```js
////// 1. typeof
function processValue(value: string | number) {
  if (typeof value === "string") {
    console.log("It's a string:", value.toUpperCase()); // Narrowed to string
  } else {
    console.log("It's a number:", value.toFixed(2)); // Narrowed to number
  }
}
////// 2. instanceof
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
/////// 3. in
type Shape = { radius: number } | { sideLength: number };
function getShapeInfo(shape: Shape) {
  if ("radius" in shape) { //check existence of a property in an object
    console.log("Circle with radius:", shape.radius); // Narrowed to { radius: number }
  } else {
    console.log("Square with side length:", shape.sideLength); // Narrowed to { sideLength: number }
  }
}
/////// 4. is
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
