


# javascript cheat sheet


https://www.javascriptcheatsheet.org/
https://quickref.me/javascript.html

img:
https://github.com/Pradeep-Pandey13/CheatSheets-HTML-CSS-JavaScript

https://github.com/logeshpaul/Frontend-Cheat-Sheets?tab=readme-ov-file



--------------
https://github.com/mbeaudru/modern-js-cheatsheet   // es6
https://github.com/krishnr/JavaScript-cheat-sheet // es5 and es6
https://github.com/lifeparticle/JS-Cheatsheet    // topic to chatgpt
https://github.com/xremix/JS-Cheatsheet   // source es6
https://github.com/bobleesj/javascript-cheatsheet  // opp

### Variable
+ A `statement` performs an action and typically end with a semi-colon, like if statement and declaration statement.
+ An `expression` is a piece of code that evaluates to a value with operations and returns a value like let x = a + 5;
+ A `block` in JavaScript is a set of statements enclosed in curly braces {},Blocks are used to group multiple statements together, often in control structures like if, for, and while
+ The `scope` of a variable roughly means "where is this variable available in the code".
+ var declared variables are moved to the top of the scope at execution. This is what we call var `hoisting`.
+ By contrast with var variables, if you try to read or write on a let or const variable before they are assigned an error will be raised. This phenomenon is often called Temporal dead zone or `TDZ`.
<table>
  <tr>
    <th></th>
    <th>Scope</th>
    <th>Reassignable</th>
    <th>Mutable</th>
   <th>Temporal Dead Zone</th>
  </tr>
  <tr>
    <th>const</th>
    <td>Block</td>
    <td>No</td>
    <td><a href="#const_mutable_sample">Yes</a></td>
    <td>Yes</td>
  </tr>
  <tr>
    <th>let</th>
    <td>Block</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>Yes</td>
  </tr>
   <tr>
    <th>var</th>
    <td>Function</td>
    <td>Yes</td>
    <td>Yes</td>
    <td>No</td>
  </tr>
</table>

```js
console.log(bar)    // var ignores block scope:
{  // standalone block 
    var bar = "bar"
}
function myFunction() {  
  var myVar = "Nick";
  if (true) {  // In JavaScript, if creates a block scope
    var myVar = "John";
    console.log(myVar); // "John"
    // actually, myVar being function scoped, we just erased the previous myVar value "Nick" for "John"
  }
  console.log(myVar); // "John" - see how the instructions in the if block affected this value
}
console.log(foo)
if(true){  // The if statement can create a block of code
    var foo="foo"
}
console.log(myVar); // Throws a ReferenceError, myVar is not accessible outside the function.
```



### Bitwise operator
```js
&	AND                     5 & 1 (0101 & 0001)     1 (1)
|	OR                      5 | 1 (0101 | 0001)     5 (101)
~	NOT                     ~ 5 (~0101)             10 (1010)
^	XOR                     5 ^ 1 (0101 ^ 0001)     4 (100)
<<	left shift              5 << 1 (0101 << 1)      10 (1010)
>>	right shift             5 >> 1 (0101 >> 1)      2 (10)
>>>	zero fill right shift   5 >>> 1 (0101 >>> 1)	2 (10)
```
### speacial valuse
```js
let a;         // undefined
let b = null
let c = 45/ 0   // Infinite
console.log(typeof NaN);  // "number"
console.log(0 / 0);         // NaN
console.log(Math.sqrt(-1)); // NaN
console.log('hello' / 2);   // NaN
```
### Type casting (type conversion)

1. Implicit Type Casting (Coercion)  ==> automatic
```js
let result = 'The number is ' + 42;  // Number is converted to string
let sum = '5' - 2;  // String '5' is converted to a number
let a= null + 5;    //5
let a = true + 1; // 2
let a = false + 5;  // 5 
if(-1){}       // -1 is converted to true
if(0){}               // Number 0 is converted to false
```
3. Explicit Type Casting
```js
let str = String(500)   // "500"
let str = num.toString();  // "500"
let num = Number("500");   // 500
let num = +"500";         // 500  using unary operator   // Unary Operators work on a single operand
let num = parseInt("10.5"); // 10
let num = parseFloat("10.5"); // 10.5
let b = Boolean('');     // false
```

### Truthy / Falsy
```js
// 0, "", null, undefined, NaN, false  ===> false
let a  = !5;       // false
let a = !NaN;      // true
```

### Order of Operations

### Strict mode
```js
function example() {
    'use strict';         // strict mode in function
    console.log(this); // this in strict mode inside a function is undefined
}
/// Restrictions of Strict Mode
x = 42; // ReferenceError: x is not defined
let obj = {
    prop: 1,
    prop: 2  // SyntaxError: Duplicate data property in object literal
};
function test(a, a) { } // SyntaxError: Duplicate parameter name not allowed in this context
let num = 012; // SyntaxError: Octal literals are not allowed in strict mode.
let a = 10;
delete a; // SyntaxError: Delete of an unqualified identifier in strict mode
eval("var a = 10;");        // SyntaxError: eval is not allowed in strict mode
with (Math) { cos = cos * 2; } // SyntaxError: Strict mode code may not include a with statement
function example(x) {
    arguments[0] = 20; // Strict mode prevents modification of arguments object
}
```

### strings
```js
var abc = "abcdefghijklmnopqrstuvwxyz";
var esc = 'I don\'t \n know';   // \n new line
var len = abc.length;           // string length
abc.indexOf("lmno");            // find substring, -1 if doesn't contain 
abc.lastIndexOf("lmno");        // last occurance
abc.slice(3, 6);                // cuts out "def", negative values count from behind
abc.replace("abc","123");       // find and replace, takes regular expressions
abc.toUpperCase();              // convert to upper case
abc.toLowerCase();              // convert to lower case
abc.concat(" ", str2);          // abc + " " + str2
abc.charAt(2);                  // character at index: "c"
abc[2];                         // unsafe, abc[2] = "C" doesn't work
abc.charCodeAt(2);              // character code at index: "c" -> 99
abc.split(",");                 // splitting a string on commas gives an array
abc.split("");                  // splitting on characters
128.toString(16);               // number to hex(16), octal (8) or binary (2)
```

### Numebr methods
```js
var pi = 3.141;
pi.toFixed(0);          // returns 3
pi.toFixed(2);          // returns 3.14 - for working with money
pi.toPrecision(2)       // returns 3.1
pi.valueOf();           // returns number
Number(true);           // converts to number
Number(new Date())      // number of milliseconds since 1970
parseInt("3 months");   // returns the first number: 3
parseFloat("3.5 days"); // returns 3.5
Number.MAX_VALUE        // largest possible JS number
Number.MIN_VALUE        // smallest possible JS number
Number.NEGATIVE_INFINITY// -Infinity
Number.POSITIVE_INFINITY// Infinity

```

### Math
```js
var pi = Math.PI;       // 3.141592653589793
Math.round(4.4);        // = 4 - rounded
Math.round(4.5);        // = 5
Math.pow(2,8);          // = 256 - 2 to the power of 8
Math.sqrt(49);          // = 7 - square root 
Math.abs(-3.14);        // = 3.14 - absolute, positive value
Math.ceil(3.14);        // = 4 - rounded up
Math.floor(3.99);       // = 3 - rounded down
Math.sin(0);            // = 0 - sine
Math.cos(Math.PI);      // OTHERS: tan,atan,asin,acos,
Math.min(0, 3, -2, 2);  // = -2 - the lowest value
Math.max(0, 3, -2, 2);  // = 3 - the highest value
Math.log(1);            // = 0 natural logarithm 
Math.exp(1);            // = 2.7182pow(E,x)
Math.random();          // random number between 0 and 1
Math.floor(Math.random() * 5) + 1;  // random integer, from 1 to 5
```

### Array methods
```js
dogs.toString();                        // convert to string: results "Bulldog,Beagle,Labrador"
dogs.join(" * ");                       // join: "Bulldog * Beagle * Labrador"
dogs.pop();                             // remove last element
dogs.push("Chihuahua");                 // add new element to the end
dogs[dogs.length] = "Chihuahua";        // the same as push
dogs.shift();                           // remove first element
dogs.unshift("Chihuahua");              // add new element to the beginning
delete dogs[0];                         // change element to undefined (not recommended)
dogs.splice(2, 0, "Pug", "Boxer");      // add elements (where, how many to remove, element list)
var animals = dogs.concat(cats,birds);  // join two arrays (dogs followed by cats and birds)
dogs.slice(1,4);                        // elements from [1] to [4-1]
dogs.sort();                            // sort string alphabetically
dogs.reverse();                         // sort string in descending order
x.sort(function(a, b){return a - b});   // numeric sort
x.sort(function(a, b){return b - a});   // numeric descending sort
highest = x[0];                         // first item in sorted array is the lowest (or highest) value
x.sort(function(a, b){return 0.5 - Math.random()});     // random order sort
// es6
const numbers = [0, 1, 2, 3, 4, 5, 6];
const doubledNumbers = numbers.map((item,index,array) => item * 2); // [0, 2, 4, 6, 8, 10, 12]
const evenNumbers = numbers.filter((item,index,array) => item % 2 === 0); // [0, 2, 4, 6]
const sum = numbers.reduce((preValue, currentValue, currentIndex, array) => preValue + currentValue, 5); // 5 is initial value and by default is the first element of array
const greaterThanFour = numbers.find((item,index,array) => item>4); // 5  //returns the first element
 // these methods takes a callback function to execute for each element in the array.   [callback(0), callback(1), callback(2), ...]
const students = [  
  { name: "Nick", grade: 10 },
  { name: "John", grade: 15 },
  { name: "Julia", grade: 19 },
  { name: "Nathalie", grade: 9 },
]; // no need to foreach, these methods are composable
const aboveTenSum = students
  .map(student => student.grade) // we map the students array to an array of their grades
  .filter(grade => grade >= 10) // we filter the grades array to keep those 10 or above
  .reduce((prev, next) => prev + next, 0); // we sum all the grades 10 or above one by one
```
### Dates
```js
Sat Jul 08 2023 12:18:24 GMT+0530 (India Standard Time)
var d = new Date();
1688798904720 miliseconds passed since 1970
Number(d) 
Date("2017-06-23");                 // date declaration
Date("2017");                       // is set to Jan 01
Date("2017-06-23T12:00:00-09:45");  // date - time YYYY-MM-DDTHH:MM:SSZ
Date("June 23 2017");               // long date format
Date("Jun 23 2017 07:45:00 GMT+0100 (Tokyo Time)"); // time zone
```
### Get Times
```js
var d = new Date();
a = d.getDay();     // getting the weekday

getDate();          // day as a number (1-31)
getDay();           // weekday as a number (0-6)
getFullYear();      // four digit year (yyyy)
getHours();         // hour (0-23)
getMilliseconds();  // milliseconds (0-999)
getMinutes();       // minutes (0-59)
getMonth();         // month (0-11)
getSeconds();       // seconds (0-59)
getTime();          // milliseconds since 1970
var d = new Date();
d.setDate(d.getDate() + 7); // adds a week to a date

setDate();          // day as a number (1-31)
setFullYear();      // year (optionally month and day)
setHours();         // hour (0-23)
setMilliseconds();  // milliseconds (0-999)
setMinutes();       // minutes (0-59)
setMonth();         // month (0-11)
setSeconds();       // seconds (0-59)
setTime();          // milliseconds since 1970)
```

### Global functions
```js
eval();                     // executes a string as if it was script code
String(23);                 // return string from number
(23).toString();            // return string from number
Number("23");               // return number from string
decodeURI(enc);             // decode URI. Result: "my page.asp"
encodeURI(uri);             // encode URI. Result: "my%page.asp"
decodeURIComponent(enc);    // decode a URI component
encodeURIComponent(uri);    // encode a URI component
isFinite();                 // is variable a finite, legal number
isNaN();                    // is variable an illegal number
parseFloat();               // returns floating point number of string
parseInt();                 // parses a string and returns an integer
```
### Object
```js

Object.keys(obj);                         // Get an array of object's own property names
Object.values(obj);                       // Get an array of object's own property values
Object.entries(obj);                      // Get an array of key-value pairs

Object.freeze(obj);                       // Prevent modification of properties and values
Object.seal(obj);                         // Prevent adding or deleting properties, but allow value modification
Object.assign(target, source);            // Copy properties from source object(s) to target object
Object.create(proto, [propertiesObject]); // Create a new object with the specified prototype

Object.getPrototypeOf(obj);               // Get the prototype of the object
Object.setPrototypeOf(obj, prototype);    // Set the prototype of the object
Object.hasOwnProperty("prop");            // Check if object has a property (excluding prototype chain)

Object.is(value1, value2);                // Check if two values are strictly equal (edge-case-safe)
Object.isExtensible(obj);                 // Check if new properties can be added to the object
Object.isFrozen(obj);                     // Check if the object is frozen
Object.isSealed(obj);                     // Check if the object is sealed

Object.defineProperty(obj, prop, desc);   // Add or modify a property on an object
Object.defineProperties(obj, props);      // Define multiple properties at once

Object.getOwnPropertyDescriptor(obj, prop); // Get descriptor object for a property
Object.getOwnPropertyDescriptors(obj);      // Get all property descriptors for the object
Object.getOwnPropertyNames(obj);            // Get all properties, including non-enumerable (excluding symbols)
Object.getOwnPropertySymbols(obj);          // Get an array of the object's symbol properties

```

### For
```js
// for-in = Object.keys() -----> over enumerable properties
for (index in {a:1, b:2}) {}   // a,b
for (index in [1,20,30,40]) {} // 0,1,2,3   // do not use for-in for arrays
for (index in "str") {}    // 0,1,2      // do not use
//// for-of = foeeach  ----> iterable
for (index of [1,20,30,40]) {} // 1,20,30,40
for (index of "str") {}    // s,t,r
```

### Symbol
+ immutable primitive value
+ Symbols are often used as property object keys that are hidden.
+ The `global Symbol registry` in JavaScript is a shared space where symbols created using the `Symbol.for()`. Note that the "global Symbol registry" is only a fictitious concept and may not correspond to any internal data structure in the JavaScript engine.
```js
const sym = Symbol('this is a description');  //  Symbol('description')   // sym is a non-registered symbol
const sym = Symbol();         // description is optional
console.log(sym.description);              //  'this is a description'  || undefined
//  symboles are immutable. And they cannot be implicitly converted to strings or numbers, preventing accidental reuse or collisions.
const obj = {
  [sym]: 'value',   // symbole is not enumerable so it's not accessible by for-in
};
Symbol('id') === Symbol('id'); // false // Each Symbol value is unique, even if they have the same description.
Symbol.for('shared') === Symbol.for('shared'); // true // symbole.for() creates a symbol available in a global symbol registry list
let a = Symbol.for('a'); // a is a shared or registered symbol
Symbole.keyFor(a); // 'a'  // get description of symbole.for()
```

### garbage collection
garbage collection is the process of automatically freeing up memory by removing objects that are no longer accessible or needed by the program.
Techniques like `weak references` (e.g., WeakMap, WeakSet) allow objects to be collected even if they are referenced by these specialized collections.
```js
let obj = { name: "John" }; // 'obj' references an object in memory
obj = null;  // 'obj' is set to null, so the object becomes unreachable
global.gc();       // in Node.js we can take control of the garbage collector
```
### Map

```js
const map1 = new Map();   // The Map object holds [key-value] pairs
map1.set('a', 1);  //  A key in the Map may only occur once [unique keys]
map1.set(true, 2);     // key in map can be any type unlike objects.  // any type like true, 23, [1,2,3], {a:1}, function(){}
map1.set('c', 3);     // Map keeps the order of keys
console.log(map1);    // Map(3) { 'a' => 1, 'b' => 2, 'c' => 3 }
map1.get('a');        // 1
map1.has('a');        // true
map1.size;            // 3
map1.delete('a');     // true
map1.clear();        // remove all elements
const map2 = new Map([['a', 1], ['b', 2], ['c', 3]]);  // { 'a' => 1, 'b' => 2, 'c' => 3 }
const keyValuePairs = Array.from(map2);    // convert to array [['a', 1], ['b', 2], ['c', 3]]
const obj = Object.fromEntries(map1);      // convert to object { 'a' => 1, 'b' => 2, 'c' => 3 }
```
#### WeakMap
+ The keys in a WeakMap must always be objects (or instances of classes). Primitives like strings, numbers, and booleans cannot be used as keys.
+ What makes a `WeakMap` different from a regular `Map` is that the keys in a WeakMap are weakly referenced. This means that if an object stored as a key in the WeakMap is no longer referenced anywhere else in the program, it can be garbage-collected, even if it is still part of the WeakMap. This avoids memory leaks and reduces the amount of memory used.
```js
let a = new WeakMap();
let obj = { name: "Alice" };
a.set(obj, 'Developer');  // key is an object
console.log(a.get(obj));
obj = null;
console.log(a);   // undefined
```
##### Usecases of WeakMap
```js
// 1. Avoiding Memory Leaks in DOM Manipulations
const elementMap = new WeakMap();
const div = document.createElement('div');
elementMap.set(div, { clicked: 0 }); // Later when div is removed from DOM, the data in `elementMap` is automatically cleaned up
// 2. Storing very private data
```

### Set
+ A JavaScript Set is an object collection of unique values with order of insertaion. 
+ Set objects are iterable and we can loop through them using `for-of`.
```js
const letters = new Set();
const letters = new Set(["a","b","c"]);
console.log(letters);   // Set(3) { 'a', 'b', 'c' }
letters.has("a"); // true
letters.has("d"); // false
letters.delete("a"); // true
letters.add("d"); // true
letters.add("d"); // true // If you add equal elements, only the first will be saved
letters.size; // 3
// usage: removine duplicate elements
let numbers = [1, 2, 3, 4, 4, 5];
let uniqueNumbers = [...new Set(numbers)];
```

#### WeakSet
WeakSets unlike sets:
+ are collections of objects and symbols only.
+ The WeakSet is weak, meaning references to objects in a WeakSet are held weakly.
```js
let weakSet = new WeakSet();
let obj = { name: 'John' };
weakSet.add(obj);  // Adds 'obj' to the WeakSet
console.log(weakSet);   // WeakSet { { name: 'John' } }
```

### Proxy
+ A Proxy is essentially a wrapper around a target object, enabling you to control the behavior of that object through traps.
+ The Proxy object allows you to create an object that can be used in place of the original object
+ used to log property accesses, validate, format, or sanitize inputs
```js
const target = {
  name: "John",
  age: 30,
};

const handler = {    
  get: function (obj, prop) {    // methods in handler called trap, defines the behavior of the proxy for specific operations.
    console.log(`Property ${prop} has been accessed.`);
    return prop in obj ? obj[prop] : `Property "${prop}" does not exist.`;
  },
};

const proxy = new Proxy(target, handler);
console.log(proxy);         // {name: "John", age: 30}
console.log(proxy.name); // Logs: Property name has been accessed. Returns: John
console.log(proxy.age); // Logs: Property age has been accessed. Returns: 30
console.log(proxy.gender); // Logs: Property gender has been accessed. Returns: Property "gender" does not exist.
```

#### 1. set trap in proxy
```js
const handler = {
  set: (target, prop, value) => {
    console.log(`Setting ${prop} to ${value}`);
    target[prop] = value;
    return true; // return true to signal success
  },
};

const obj = {};
const proxy = new Proxy(obj, handler);

proxy.name = "Bob"; // Logs: Setting name to Bob
console.log(proxy.name); // Bob
```

#### 2. constructor trap in proxy
```js
const handler = {
  construct: (target, args) => {
    console.log(`Constructor called with arguments: ${args}`);
    return new target(...args);
  },
};

class Person {
  constructor(name) {
    this.name = name;
  }
}

const proxyPerson = new Proxy(Person, handler);
const john = new proxyPerson("John"); // Logs: Constructor called with arguments: John
console.log(john.name); // John
```
####  3. apply trap in proxy
```js
const handler = {
  apply: (target, thisArg, args) => {
    console.log(`Function called with arguments: ${args}`);
    return target.apply(thisArg, args);
  },
};

const target = (x, y) => x + y;
const proxy = new Proxy(target, handler);

console.log(proxy(2, 3)); // Logs: Function called with arguments: 2,3. Returns: 5
```

#### 4. has trap, delete trap , .....
```js
new Proxy(target, handler);               // Create a proxy with a target and handler object
// Common Proxy Handlers
get(target, prop, receiver);              // Intercept property access
set(target, prop, value, receiver);       // Intercept property assignment
has(target, prop);                        // Intercept 'in' operator
deleteProperty(target, prop);             // Intercept 'delete' operator
ownKeys(target);                          // Intercept Object.keys() and similar methods
apply(target, thisArg, args);             // Intercept function calls
construct(target, args, newTarget);       // Intercept object construction
defineProperty(target, prop, descriptor); // Intercept Object.defineProperty()
getOwnPropertyDescriptor(target, prop);   // Intercept Object.getOwnPropertyDescriptor()
preventExtensions(target);                // Intercept Object.preventExtensions()
isExtensible(target);                     // Intercept Object.isExtensible()
getPrototypeOf(target);                   // Intercept Object.getPrototypeOf()
setPrototypeOf(target, proto);            // Intercept Object.setPrototypeOf()
```


### Reflect
+  The Reflect namespace object contains static methods [have the same names as the proxy handler methods] for invoking interceptable JavaScript object internal methods and is designed to complement the Proxy object.
+  Some methods are like to `Object` method.

#### 1. Reflect.construct
```js
class Person {
  constructor(name) {
    this.name = name;
  }
}
const john = Reflect.construct(Person, ["John"]);  // invoking the constructor as a function
console.log(john); // Person { name: 'John' }
let foo= new Person("foo")    // 
```

#### 2. Reflect.defineProperty
```js
const object1 = {};
if (Reflect.defineProperty(object1, 'property1', { value: 42 })) {
  console.log('property1 created!');   // Expected output: "property1 created!"
} else {
  console.log('problem creating property1');    // reflect.defineProperty() returned true or false
}
console.log(object1.property1);  // Expected output: 42
```

#### 3. Reflect.deleteProperty
```js
const object1 = { property1: 42 };
Reflect.deleteProperty(object1, 'property1');  // like delete operator  but as a function
console.log(object1.property1);  // Expected output: undefined
///
const array1 = [1, 2, 3, 4, 5];
Reflect.deleteProperty(array1, '3');
console.log(array1);  // Expected output: Array [1, 2, 3, undefined, 5]
```

#### 4. Reflect.*
```js
//  Property Operations
Reflect.get(target, prop, [receiver]);          // Get property value from target
Reflect.set(target, prop, value, [receiver]);  // Set property value on target
Reflect.has(target, prop);                     // Check if prop exists on target
Reflect.deleteProperty(target, prop);          // Delete a property from target   // like delete operator
// Object Metadata
Reflect.ownKeys(target);                       // Get all property keys (own, enumerable, non-enumerable, symbols)
Reflect.getOwnPropertyDescriptor(target, prop); // Get descriptor of a property
Reflect.defineProperty(target, prop, desc);    // Define a new property or modify existing one  // like Object.defineProperty
Reflect.isExtensible(target);                  // Check if an object is extensible
Reflect.preventExtensions(target);             // Prevent new properties on an object
Reflect.getPrototypeOf(target);                // Get the prototype of an object
Reflect.setPrototypeOf(target, proto);         // Set the prototype of an object
// Function and Constructor Operations
Reflect.apply(func, thisArg, args);            // Call a function with a specific this and arguments
Reflect.construct(target, args, [newTarget]);  // Call a constructor with arguments   // like new operator: new target(...args)
```
#### 5. Reflect and Proxy
It allows you to invoke default behavior when trapping methods in proxies.
```js
const target = {
  message1: "Hello",
  message2: "World"
};

const handler = {
  get(obj, prop) {
    console.log(`Accessing property: ${prop}`);
    return Reflect.get(obj, prop); // Default behavior
  }
};

const proxy = new Proxy(target, handler);
console.log(proxy.message1); // Logs: Accessing property: message1, Output: Hello
console.log(proxy.message2); // Logs: Accessing property: message2, Output: World
```

### Error name values
```bash
RangeError      A number is "out of range"
ReferenceError  An illegal reference has occurred
SyntaxError     A syntax error has occurred
TypeError       A type error has occurred
URIError        An encodeURI() error has occurred
```


### Functions

1. How to define a function?

```js
function square(x) { return x * x; }             // 1. function declaration
const square = function(x) { return x * x; };    // 2. function expression  with  anonymous function
const square2 = function sq(x) { return x * x; };    // function expression with name  (using declaration)
let add = (a, b) => a + b;                              // arrow function expression
sq()          // error: sq is not defined
(function() {                                            // self-invoked function expression 
    console.log('IIFE');  // You cannot self-invoke a function declaration
})();
const sum = new Function('a', 'b', 'return a + b');    // 3. the Function constructor [Function(), GeneratorFunction(), AsyncFunction()]
```
1. function hoisting

```js
log()                    // This code runs without any error
hello()                 // Function hoisting only works with function declarations — not with function expressions
function log(){
    console.log("log")
}
const hello = function() { console.log("hello") }
```
1. Recursion

```js
const foo = function bar(){   // a function that calls itself called reursive function
    bar()          // bar() is not accessible out of here
    foo()           // these three calls are equivalent
    arguments.callee()     
}
```

1. Clouser
+ A closure is an expression (most commonly, a function) that can have free variables together with an environment that binds those variables
+ the inner function can use the arguments and variables of the outer function, while the outer function cannot use the arguments and variables of the inner function.
```js
function outside(x) {
  function inside(y) {
    let a = "a";                // clouser provides encapsulation for this variable since the outer does not access to it
    return x + y;
  }
  return inside;
}
outside(5)(10)   // 15   // specify arguments for both the outer and inner function
const funcInside = outside(5)  // returns [Function: inside] and preserves all the variables in the outer and inner
funcInside(10)   // 15
```
2. Name conflict in closures

```js
function outer() {
    let a = 4
    let b = "b"
    function inner() {
        a =5
        let b ="c"      // there is no way to access b in the outer function anymore, since it overwrote it
    }
    inner()
    a++;
    console.log(a , "outer");    // 6
}
function outside() {
    const x = 5;
    function inside(x) {
      return x * 2;
    }
    return inside;
}  
console.log(outside()(10)); // 20 (instead of 10)  
```
1. Function parameters

```js
// 1. arguments object
function myFunc() {   //  array-like object
  console.log(arguments); // [Arguments] { '0': 1, '1': 2 }
}
myFunc(1, 2);  // useful if you don't know in advance how many arguments will be passed
// 2. rest parameters
function myFunc(x,y,...rest) {
    console.log(rest)   // array
}
myFunc(1,2,3,4,5)
// 3. default parameters
function myFunc(x = 10, y) {  // defautl parameter fot y is undefined
  return x;
}
console.log(myFunc(undefined)) // 10 
console.log(myFunc(null)) // null 
```

1. return 

```js
function myFunc(x) {
    return 1,2,3;   // only 3 is returned
}
```
1. function-stack = call-stack
+ call satck follows LIFO and when a function is invoked, its execution context (information about the function's state, local variables, and instructions) is added to the call stack
+ JavaScript's call stack operates in a single thread, meaning only one function is executed at a time.

5. event loop
```js
function first() {
  console.log("First function");
  setTimeout(() => console.log("Inside setTimeout"), 0);
  console.log("End of first");
}
```

### call-bind-apply
+ functions are themselves objects, and they have methods like call, apply, and bind.
+  used to control the value of `this` when invoking functions.

```js
// 1. call
function greet(greeting, punctuation) {
  console.log(`${greeting}, ${this.name}${punctuation}`);
}
const person = { name: "Alice" };  // the first argument of call refers to `this`
greet.call(person, "Hello", "!");   // Hello, Alice!   // Invokes (calls) a function immediately
// 2. apply
function greet(greeting, punctuation) {   // apply is the same as call
  console.log(`${greeting}, ${this.name}${punctuation}`);
}
const person = { name: "Bob" };    // the first argument of apply refers to `this`
greet.apply(person, ["Hi", "."]); // Hi, Bob.   // arguments passed as an array
// 3. bind
function greet(greeting, punctuation) {
  console.log(`${greeting}, ${this.name}${punctuation}`);
}
const person = { name: "Carol" };  // the first argument of bind refers to `this`
const boundGreet = greet.bind(person, "Hey");  // Returns a new function with this, and does not excecute it immediately like call and apply
boundGreet("!"); // Hey, Carol!   // we can pass arguments to the new function or not
```

### arrow-function
+ `No Binding of this`: Arrow functions do not have their own `this`
+ every new function defined its own `this` but arrow functions inherit `this` from the enclosing lexical scope
+ does not have its own `this`, `arguments`, `super`, or `new.target`
+ Arrow functions are always anonymous.

```js
const myFunc = x => x * 2; // function written as an arrow function with implicit return
const getPerson = () => ({ name: "Nick", age: 24 })   // implicit return an object. // add () to avoid conflict
const MyClass = () => {};  // arrow functions cannot Be Used as Constructors
const obj = new MyClass();  // gets error
const showArgs = (...args) => {}// we can not use arguments with arrow funcions
function ArrowFunction() {
  this.value = 42;
  setTimeout(() => {
   // In an arrow function, this is equal to the this value of the enclosing execution context.
   //  with arrow functions, you don't have to do the "that = this" trick before calling a function inside a function anymore.
    console.log(this.value); // Outputs: 42 because arrow functions inherit `this`.
  }, 1000);
}
```

### This

1. Global Context

```bash
Browser           # window
node              # In nodejs each file is treated as its own module and this refers to module.exports, which is {} by default.
strict mode       # undefined
html onclick attribute               # element
```
2. OOP

```js
class Person {
    constructor(name) {
        this.name = name;    // refers to the instance of the object
    }
    greet() {
        console.log(this.name);   // In an object method, this refers to the object.
    }
}
```

3. Functions 

```js
// 1. strict mode           # undefined
// 2. arrow function         # inherited from outer scope
// 3. regular function                # global object
// 4. Event Handler function           # element
// 5. constructor function               #  When you call a function with the new keyword, it becomes a constructor function
function myFunc() {  // myFunc is a constructor function
    this.name = "foo";   // this refers to myFunc
    console.log(this);    // { name: 'foo' }
    self = this;           // self trick
    function inner(){
        console.log(this)   // this refers to global in nonstrict mode and this is not good for oop
        console.log(self)   // self refers to myFunc
    } // or we  can use arrow functions since they inherit `this` from the enclosing lexical scope
}
let p = new myFunc();
```
4. Explicit Binding

```js


```

5. node 

```bash
node            # {}
function          # gloabl object
global            # global is a variable that refers to global object in nodejs
```

### ES module : Export and Import
+ Each JavaScript file is treated as a module.
+ Code inside a module is automatically in strict mode
```js
//  Modules are automatically interpreted in strict mode.
// es5 commonjs
export const a ="variable"    // error
export {a,b}; // Error 
module.exports = {a,b}       //
module.exports = a             // defult export
const a = require('./A.js');  // 
// es5 AMD  (Asynchronous Module Definition)
define(() => {
    return {
        add: (a, b) => a + b,
        subtract: (a, b) => a - b,
    };
});
require(['math'], (math) => {
    console.log(math.add(2, 3));  // Output: 5
});
// es6
import a, { b, c } from './A.js';   // named export // a is default export 
import * as all from './A.js';
export * from './A.js';      // we can re-export from another file
export { a };
```

### Iterators and Generators
+ An iterator is an object that adheres to the iterator protocol.
+ An object is an iterator if it has a next() method that returns an object with two properties:
a. value : The value of the current iteration
b. done : A Boolean indicating whether the iteration is complete
```js
let iterator = {  // iterator is an object
  next() {    // next is a method    // next returns an object
    return { value: 1, done: false }
  }
};
// we can create a custome Iterator
```
we can create a custome iterator but generators are a special type of function in JavaScript that makes creating iterators much easier. It is defined using the `function*` syntax and automatically conforms to the iterator protocol. Instead of `return` they `yeild` values and they can be paused and resumed

```js
function* myGenerator() {  // myGenerator is a generator(function)
  yield;
  yield 2;
  yield 3;
  yield* genB(i);   // The yield* expression enables a generator to call another generator function during iteration.
}
const gen = myGenerator();   // gen is an iterator(object)
console.log(gen.next()); // { value: undefined, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: undefined, done: true }
```

Generators are iterators, but they're also iterable, which means you can use them with constructs like `for...of` and the `spread operator`.

```js
function* numbers() {
  yield 1;
  yield 2;
  yield 3;
}
for (const num of numbers()) {
  console.log(num); // Logs 1, 2, and 3
}
console.log([...numbers()]); // [1, 2, 3]
// infinite iterator
const infinite = function* () {   // functction* expersion
  let index = 0;
  while (true) {
    yield index++;
  }
}
```
### Enumeration
Enumeration in JavaScript refers to the process of iterating (enumerating) over the properties of an object.
We can enumerate over the properties of an object by using the `for...in` or `Object.keys()` and so on.

```js
// non-enumerable properties are accessible programmatically but not with these methods.
const mySymbol = Symbol('mySymbol');
const obj = {name: "foo"};   // by default it`s enumerable
Object.defineProperty(obj, mySymbol, {  // this property is not visible since it`s not enumerable
  value: 'Symbol value',  
  enumerable: false,    // non-enumerable property
});
console.log(obj.propertyIsEnumerable(mySymbol)); // false => so it`s not accessible with `for...in`
```

### Proxy

### reflect

### Anamorphisms and Catamorphisms


### Try-Catch

```js
function divide(a,b){   // a/b
  if (b ==0) throw new Error("pls do not enter zero")  // throw Error make our app crash
}
try{
  result = divide(1,0)
  console.log(result)              // this line do not run bcz throw error 
}catch(error){                    // catch prevents our app from crash
    console.log(error.name, error.message)    //message is our personal log inside Error obj
}finally{
    // finally is benefical when we have 'return' in our catch and the whole try-catch is inside a function
}
```

## Callback
+ callback is a function that is passed as an argument to another function and is executed at a later time
+ we use callback for event-handling, Timers, API request
+ Disadvantages of Callbacks: 1. callback hell(nested) 2. Error handling
+ we can use Promise or Async/Await instead bcz asynchronus programmes are difficult to debug.


## Promise
A promise is an object which can be returned synchronously from an asynchronous function which have three different states: pending-fullfiled-rejected
```js
const prom = new Promise(muFunc); //  is an object used to wrap callback-based APIs
const myFunc =(resolve, reject) => {  // this called executor and receives two functions as parameters
  setTimeout(() => {           // promises use for operations that take time (async) and returns a value that is not necessarily known
        x =0;
        if (x == 0) {
            resolve("OK");    // resolve is a pre-defined by JavaScript and returns our message to then()
        } else {
            reject("My Error");
        }
    }, 300);
}

prom.then((value) => { //As these methods (then-catch-finally) return promises, they can be chained
  console.log(value);   // Expected output: "Ok"
}).catch(function(error){
    console.log(error);  // "My Error"
});
```

## Async-Await
```js
// async before a function makes the function return a promise
async function hello() {
    return("Hello")  // Is the same as: return Promise.resolve("Hello");
}
hello().then((msg)=>{console.log(msg)})   // prints "Hello"
// await keyword makes the function pause the execution and wait like [let value = await promise;]
```

## fetch

```js
async function getData() {  
  const url = "https://example.org/products.json";
  try {
    const response = await fetch(url);        // fetch() is a function and returns a promise
    if (!response.ok) {                    // it`s not automatic like axios
      throw new Error(`Response status: ${response.status}`); // this Error will get inside catch
    }

    const json = await response.json();  // json() is async like fetch(), and the response is a stream
    console.log(json);                  // console is not async so we should use await
  } catch (error) {
    console.error(error.message);     
  }
}
```

## request

```js
const request = new Request("https://example.org/post", {   // request() represents a resource for fetch
  method: "POST",
  body: JSON.stringify({ username: "example" }),
});
console.log(request.url,request.method,request.bodyUsed, request.credentials) // req is an object
fetch(request) // pass request to fetch()
  .then((response) => response.blob())
  .then((blob) => {
    image.src = URL.createObjectURL(blob);
});

```

# Binary objects in js


## 1. ArrayBuffer 
. ArrayBuffer is a memory area [ a reference to a fixed-length contiguous memory area(RAM)]
. The basic binary object is ArrayBuffer and is used to keep binary data( binary data of an image for example)

```js
// Arraybuffer is not array of something and we cannot access by buffer[5]
let buffer = new ArrayBuffer(16); // create a buffer of length 16 byte and the content is initialized to 0
console.log(buffer , buffer.byteLength); // returns a new ArrayBuffer object //16 
```
. To manipulate an ArrayBuffer, we need to use a "TypedArray" object or "Dataview".
. There’s no constructor called TypedArray, it’s just a common “umbrella” term to represent one of views over ArrayBuffer: Int8Array, Uint8Array and so on.
.Typed arrays behave like regular arrays: have indexes and are iterable.
. Int8Array stores each item in 8 byte, Int32Array stores in 32 byte

```js
const buffer = new ArrayBuffer(8);  // Create an ArrayBuffer of 8 bytes
const uint8View = new Uint8Array(buffer); // Create a Uint8Array view on the buffer
uint8View[0] = 255;  // Write data to the ArrayBuffer // Maximum value for Uint8 is 255
console.log(uint8View[0]); // 255 // for bigger numbers we will overflow
```
. DataView provides more flexibility
. With DataView we access the data with methods like .getUint8(i) or .getUint16(i). We choose the format at method call time instead of the construction time.

```js
const buffer = new ArrayBuffer(8); // Create an ArrayBuffer of 8 bytes
const view = new DataView(buffer);  // pass arraybuffer to dataview
view.setInt32(0, 42); // Write 32-bit integer at byte 0
console.log(view.getInt32(0)); // 42
```


## 2. TextDecoder and TextEncoder

```js
let uint8Array = new Uint8Array([72, 101, 108, 108, 111]); 
let decoder= new TextDecoder()  // we can set encoder bur default in utf8
let str= decoder.decode(uint8Array) 
console.log( str ); // Hello
// TextEncoder does the reverse thing [converts a string into bytes]
let encoder = new TextEncoder();
let uint8Array = encoder.encode("Hello");
alert(uint8Array); // 72,101,108,108,111
```

## 3. Blob (Binary Large Object)
. blob is a data structure that represents a file-like object for using in file creation.
. blob is raw, immutable and has its own size and MIME type, similar to regular files
. the content of a blob can be easily read as an ArrayBuffer


```js
// download a file with blob
const blob = new Blob(["<html>…</html>"], {type: 'text/html'});  // create Blob from a string
const blob = new Blob(["This is a text"], { type: "text/plain" }); // MIME type is text
const url = URL.createObjectURL(blob); // Create a temp URL for the blob
const link = document.createElement("a");  // you can find a tag using queryselector or create one
link.href = url;
link.download = "sample.txt"; // File name(optional), download is an attribute for anchor tag
link.click(); // Trigger download automatically but if you want to do it with an existing tag you dont need it
URL.revokeObjectURL(url); // Release memory

```
## 4. file and file reader

```js
<input type="file" onchange="readFile(this)">  // it is a File object in js
<script>
function readFile(input) {
  let file = input.files[0];
  let reader = new FileReader();   //File objects inherit from Blob, so it has Blob methods and properties
  reader.readAsText(file);  // read asynchronously the contents as a text string with utf-8
  reader.onload = function() {
    console.log(reader.result);
  };
  reader.onerror = function() { // The most widely used events are for sure load and error.
    console.log(reader.error);
  };
}
</script>
```

## Base64

## ASI = automatic semicolon insertion
a. Situations Where ASI Inserts Semicolons
b. Situations Where ASI Does NOT Insert Semicolons

1.  Incomplete Statements
```js
let a = 5
let b = 10
let c = a + b
[2, 2, 3].forEach(n => console.log(n)) // Results in an error because JavaScript sees c = a + b[1, 2, 3].
d
=
a
+
b         // see d = a + b;
++
b          // see ++b;
a           // see a;
console.log(c) // 15
```
2.  Functions Returning Objects
```js
function getObject() {
  return 
  {
    a: 1
  }
}
console.log(getObject()); // undefined  // js sees return;
```
3.  Chained Method Calls
```js
let result = 'test'
.toUpperCase() // see result.toUpperCase();
console.log("sa")  //ASI automatically terminates simple expression statements.
```


## Destructuring assignment
+ unpack values from array and obj

1. object destructuring
+ In const { age } = person;, the brackets after const keyword are not used to declare an object nor a block but is the destructuring syntax.

```js
const { a, b } = obj; // const a= obj.a; const b=obj.b
person= {
    age=45,
    name = "ali",
    gender = "male"
}
const { gender } = person     // const gender = person.gender
const { age: x } = person  // const x = person.age
console.log(age)            // error age is not defined
const { name = "default" } = person // const name = person.name || "defaul
```
+ Destructuring is often used to destructure objects parameters in functions.
```js
person = {
    name : "ali",
    age: 12
}
function find({name, age}) {
    console.log(name);
    console.log(age);
}
find(person)
```
2. array destructuring

```js
let [a, b, c] = "abc"; // ["a", "b", "c"]
let arr = [1,2,3,4,5]
let [a,b, ,d,e] = arr   // in order: a=1 b=2 d=4 e=5 // use comma to skip
[name, age] = ["robin", 20];   // name= "robin"  // age=20
```

## Computed Property Names
```js
const name = "ali";
const obj = {
  [name]: "Hello",   // this will be computed and used
  name : 45
};
console.log(obj); // {ali : hello, name: 45}
```
## Object property shorthand

```js
const x = 10;
const myObj = { x };
console.log(myObj.x) // 10
let a =1
let b =2
let x ={a,b}   // x= {a:1, b:2}
let x = {     //  equivalent to this
    a:a,
    b:b
}
```

## spread operator
```js
// 1. array 2. obj  3. function
let a = [1,2,3]
let c = [1,a,2]   // [1.[1,2,3],3]
let b = [1,...a,2]   // spread and expand elements of array
// 2. obj
const { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };  // // object destructuring
console.log(x); // 1
console.log(y); // 2
console.log(z); // { a: 3, b: 4 }
const n = { x, y, ...z };
console.log(n); // { x: 1, y: 2, a: 3, b: 4 }
```


### literl
```js
let a = "sd"  // string literal
let a = [1,2,3]  // array literal
let a ={ a:1 , b:2}  // object literal
// 1. Array constructor 2. dynamically like filling a=[] with a for loop 
```

## template literal
is a way of `string interpolation` in js
```js
const name = "Nick";
`Hello ${name}, the following expression is equal to four : ${2+2}`;
// Hello Nick, the following expression is equal to four: 4
```
## Tagged template literals
like `styled-component`

```js
function myTag(strings, ...values) {
    console.log(strings);   // array of regular strings
    console.log(values);     // array of values inside template
}
let name = "js"
let age = 45
let b = myTag`we can pass ${age} strings to functions in ${name} like this`;
```

### Prototype
Every object in JavaScript has a built-in property [which is accessable with __proto__], which is called its prototype. The prototype is itself an object, so the prototype will have its own prototype, making what's called a prototype chain. The chain ends when we reach a prototype that has null for its own prototype. When you try to access a property of an object: if the property can't be found in the object itself, the prototype is searched for the property. If the property still can't be found, then the prototype's prototype is searched, and so on until either the property is found, or the end of the chain is reached, in which case undefined is returned.
```js
let a= 2;      // Number()
let a ='string';   // String()
function log(){}  // { [native code] }
```
+ Instead of using the `__proto__` property, you can use the `Object.getPrototypeOf()` method to get the prototype of an object.
```js
let obj = {
    name: "ali",
    age: 45,
    log: ()=>{console.log("hello")}
}
console.log(obj.__proto__);
console.log(obj.__proto__.__proto__);
const carl = Object.create(personPrototype);  // creates a new object and allows you to specify an object that will be used as the new object's prototype.
console.log(carl);             // {}
console.log(carl.__proto__);    //  { name: "ali", age: 45, log: [Function] }
carl.greet(); // hello!
```

#### 1. Constructor Functions
```js
function Person(name, age) {   // constructor function
    this.name = name;
    this.age = age;
    this.log = function(){ console.log(`${this.name}`)}
}
Person.prototype.gender= "male";   // we can not add a property to a constructor function without a prototype
Person.prototype.greet = function () {  // we can not add a function to a constructor function without a prototype
    console.log(`Hi, I'm ${this.name} and I'm ${this.age} years old.`);
};
const john = new Person('John', 30);
john.greet();  // Output: Hi, I'm John and I'm 30 years old.
```

### OOP in es5
JavaScript is a prototype-based language (whereas Java is class-based language, for instance). 

#### 1. Constructor Functions
```js
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.greet = function () {
        console.log(`Hi, I'm ${this.name} and I'm ${this.age} years old.`);
    };
}
const alice = new Person('Alice', 25);
alice.greet();  // Output: Hi, I'm Alice and I'm 25 years old.
```
#### 2. Prototype-Based Inheritance
```js
function Animal(type) {
  this.type = type;
}
let dog = new Animal("Dog");   // Changes to the prototype are immediately available to all instances.
Animal.prototype.makeSound = function() {   // add a method to the prototype
  console.log(this.type + " makes a sound!");
};
let cat = new Animal("Cat");  // changes to the prototype are immediately available to all instances.
dog.makeSound(); // "Dog makes a sound!"
cat.makeSound(); // "Cat makes a sound!"
```

### 3. Object Inheritance
```js
function Vehicle(type) {   // parent
    this.type = type;
    this.drive = function() {
      console.log(this.type + " is driving!");
    };
}
Vehicle.prototype.editPrototype = function() {
  console.log(this.type + " is editing!");
};
function Car(type, brand) {   // Car inherits from Vehicle
    Vehicle.call(this, type); // Call parent constructor
    this.brand = brand;
    this.honk = function() {
        console.log(this.brand + "goes honk!");
    };
}
Car.prototype = Object.create(Vehicle.prototype);   // Link prototypes to inherit methods
Car.prototype.constructor = Car;   // before this line, Car.prototype.constructor would the parent
let myCar = new Car("Car", "Toyota");
myCar.drive(); // "Car is driving!"
myCar.honk();  // "Toyota goes honk!"
myCar.editPrototype();  // without linking prototypes, myCar would not have the editPrototype method
```
#### 4. Encapsulation
```js
function Counter() {
   var count = 0; // Private variable  // using clouser to simulate encapsulation
   this.name = 'counter';   // Public variable
  this.increment = function() {
    count++;
    console.log(count);
  };

  this.decrement = function() {
    count--;
    console.log(count);
  };

  this.getCount = function() {
    return count;
  };
}

let counter = new Counter();
counter.increment(); // 1
counter.increment(); // 2
console.log(counter.getCount()); // 2
console.log(counter.name);
```

#### 5. Polymorphism
```js
function Animal() {
    this.makeSound = function() {
      console.log("Some generic animal sound");
    };
}
function Dog() {
    this.makeSound = function() {  // override
      console.log("Woof! Woof!");
    };
}
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;
let genericAnimal = new Animal();
let dog = new Dog();
genericAnimal.makeSound(); // "Some generic animal sound"
dog.makeSound();          // "Woof! Woof!"
```
#### 6. Mixins
```js
let flyable = {
    fly: function() {
      console.log(this.name + " is flying!");
    }
  };
  let swimable = {
    swim: function() {
      console.log(this.name + " is swimming!");
    }
  };  // Mixins allow you to copy methods and properties from one object to another, enabling a form of "multiple inheritance."

  function Bird(name) {
    this.name = name;
  }
  Object.assign(Bird.prototype, flyable, swimable);     // Mixin behavior
  let duck = new Bird("Duck");
  duck.fly();  // "Duck is flying!"
  duck.swim(); // "Duck is swimming!"
```
### OOP in es6

#### 1. Classes
```js
class Person {
  publicProperty = "human";   // Public fields
  constructor(name, age) {
    this.name = name; // Instance property
    this.age = age;   // Instance property
  }
  sayHello() {
    console.log(`Hi, my name is ${this.name}.`);
  }
}
const person1 = new Person("Alice", 30);
person1.sayHello(); // "Hi, my name is Alice."
```

#### 2. Inheritance
```js
class Animal {
  constructor(type) {
    this.type = type;
  }
  speak() {
    console.log(`${this.type} makes a sound.`);
  }
}
class Dog extends Animal {  // extends for inheritance
  constructor(name, breed) {
    super("Dog"); // Calls the parent constructor
    this.name = name;
    this.breed = breed;
  }
  bark() {
    console.log(`${this.name} says Woof!`);
  }
}
const myDog = new Dog("Rex", "Labrador");
myDog.speak(); // "Dog makes a sound."
myDog.bark();  // "Rex says Woof!"
```

#### 3. Getters and Setters
```js
// es5
const person = {
    firstName: "John",
    lastName: "Doe",
    language: "en",  // getter and setter can secure better data quality
    get lang() {
      return this.language;
    },
    fullName: function() {  // like getter
      return this.firstName + " " + this.lastName;  // get fullname
    },
    set langSetter(value) {  // Setter must have exactly one formal parameter.
        this.language = value;
    }
};
console.log(person.lang);  //en  // we access getter like a property
person.langSetter = "ge";   // setter  // we access setter like a property 
person.fullName(); // "John Doe"      // we access  methods like a function
// defineProperty
const obj = {counter : 0};
Object.defineProperty(obj, "reset", {  
  get : function () {this.counter = 0;}   // Define setters and getters
});
// es6 class
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }
  get area() { // Getter
    return this.width * this.height;
  }
  set width(value) {   // Setter
    if (value <= 0) {
      throw new Error("Width must be positive!");
    }
    this._width = value;
  }
  get width() {
    return this._width;
  }
}

const rect = new Rectangle(5, 10);
console.log(rect.area); // 50

rect.width = 8;
console.log(rect.area); // 80
```
#### 4. Static Methods
```js
class MathUtils {
  static add(a, b) {
    return a + b;
  }
  static name = "foo";  // static property
}
MathUtils.add(5, 10); // 15    // we access the method without creating an instance object
```

#### 5. Private Fields and Methods
```js
class Counter {
  #count = 0; // Private field
  #counter = "";  // Private field
  creator = "John";   // public filed
  _familyName = "Doe";  // protected field
  constructor(value="") {  // constructors cannot be private
      this.name = "foo";    // Instance field
      this.#counter = value;  // Private instance field
  }
  static #st = 42;  // static private 
  increment() {  
    this.#count++;
    console.log(this.#count);
  }
  #logMessage() { // Private method
    console.log("Counter value: " + this.#count);
  }
}
const counter = new Counter();
counter.increment(); // 1
counter.increment(); // 2
console.log(counter.#count); // SyntaxError: Private field '#count' must be declared in an enclosing class
```

#### 6. Protected Fields and Methods
```js
class Parent {
    #privateParent;
    _protected= "protected";   // protected property
    constructor() {
      this.#privateParent = 42;
    }
  }
  
  class Subclass extends Parent {
    #subPrivateField;
    constructor() {
      super();
      this.#subPrivateField = 23;
    }
    log = ()=>{    // public method
        console.log(this.#subPrivateField);
        console.log(this._protected);        // we access protected property
        console.log(this.#privateParent);    // error : we do not access private field in parent
    }
  }
  
  let a =new Subclass();
  a.log()
  console.log(a);    // in browser, it shows Subclass {#privateParent: 42, ...} and you can see that but it's not accessiable
  console.log(a._protected);   // it shows protected property so oop in js is not strong
```
#### 7. Polymorphism
```js
class Animal {
  makeSound() {
    console.log("Some generic animal sound");
  }
}
class Dog extends Animal {
  makeSound() {
    console.log("Woof! Woof!");
  }
}
```
#### 8. Mixins
A mixin in ES6 is a way to compose multiple behaviors (methods and properties) into a single class without using inheritance

### Event Loop
Event Loop : The event loop is the secret behind JavaScript's asynchronous 
programming. There’s an endless loop, where the JavaScript engine waits for tasks, executes them and then sleeps, waiting for more tasks
JS executes all operations on a single thread, but using a few smart data structures, it gives us the illusion of multi-threading.
Easy Definition : 
The Event Loop has one simple job — to monitor the Call Stack and the 
Callback Queue(Message Queue or macrotask queue).
If the Call Stack is empty, it will take the first event from the
Callback queue and will push it to the Call Stack, which effectively runs it. 
the browser or Node.js gives us some features that the JavaScript engine itself doesn’t provide: a Web API. This includes the DOM API, setTimeout, HTTP requests,handling multimedia, and so on.
```js
// 1. DOM API
const element = document.getElementById("myElement");   // Document API
element.addEventListener("click", () => console.log("Button clicked!"));   // element API
document.body.style.backgroundColor = "lightblue";   // CSSOM (CSS Object Model)
// 2. Networking APIs
fetch("url").then((res)=>res.json())    // fetch API
const xhr = new XMLHttpRequest();       // XMLHttpRequest API
// 3. Storage APIs
localStorage.setItem("key", "value");   // localStorage API
sessionStorage.setItem("key", "value"); // sessionStorage API
// 4. Graphics APIs
const ctx = canvas.getContext("2d");     // Canvas API
                                          // WebRTC API
                                          // WebAudio API
// 5. Device APIs
navigator.geolocation.getCurrentPosition((pos) => console.log(pos));  // Geolocation API
navigator.mediaDevices.getUserMedia({ video: true });   // MediaDevices API
navigator.serviceWorker.register("service-worker.js");   // ServiceWorker API
navigator.clipboard.writeText("Hello World!");   // Clipboard API
navigator.getBattery().then((battery) => { console.log(battery.level); });   // Battery API
navigator.permissions.query({ name: "geolocation" }).then((result) => { console.log(result.state); });   // Permissions API
// 6. Timers
setInterval(() => console.log("Hello World!"), 1000);   // setInterval API
```

### BUGs
+ https://jsisweird.com/

```js
console.log(0.1 + 0.2); // 0.30000000000000004  //Floating-Point Precision
console.log([1, 2, 3] + [4, 5, 6]);       // 1,2,34,5,6   // string
console.log(parseInt(0.0000005))    //5
console.log(true == "true")      // false
console.log(true == 1)      // true
console.log(true == 4)      // false
console.log(010 - 03);         // 5
console.log(- "");             // -0 
console.log(- - "");           // 0
console.log(0/0, 4/0);              // NaN, Infinity
console.log("" && -0)                // ""
```

