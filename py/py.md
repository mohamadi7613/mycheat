# Python


## Usefull links

https://github.com/kieranholland/best-python-cheat-sheet?tab=readme-ov-file
https://www.pythoncheatsheet.org/


## python version

```py
import sys
print(sys.implementation)             # cpython
```


### Primitive Data Types
```py
x = 10                         # Integer
print(x, type(x))               # Output: 10 <class 'int'>
y = 4.5                       # Floating-Point Number  # 4.5 <class 'float'>
z = 4 + 3j                     # Complex Number        # (4+3j) <class 'complex'>
name = "John"                  # String                # John <class 'str'> 
is_student = True               # Boolean               # True <class 'bool'>
value = None                  # None Type             # None <class 'NoneType'>
PI = 3.14                   # Constant               # 3.14 <class 'float'>   
# python does not support constants, by convention we write them in capital letters
```

### Non primitive Date types

```py
fruits = ["apple", "banana", "cherry"]        # List → ordered, mutable, allows duplicates
print(fruits, type(fruits))                   # ['apple', 'banana', 'cherry'] <class 'list'>

point = (4, 5)                                # Tuple → ordered, immutable, allows duplicates
print(point, type(point))                     # (4, 5) <class 'tuple'>

colors = {"red", "green", "blue"}             # Set → unordered, mutable, no duplicates
print(colors, type(colors))                   # {'red', 'green', 'blue'} <class 'set'>

person = {"name": "Ali", "age": 30}           # Dict → key-value pairs, mutable, no duplicates without error
print(person, type(person))                   # {'name': 'Ali', 'age': 30} <class 'dict'>

r = range(5)                                  # Range → sequence of numbers (0 to 4)
print(list(r), type(r))                       # [0, 1, 2, 3, 4] <class 'range'>

b = bytes("hello", "utf-8")                   # Immutable bytes object
print(b, type(b))                             # b'hello' <class 'bytes'>

ba = bytearray("hello", "utf-8")              # Mutable bytes object
print(ba, type(ba))                           # bytearray(b'hello') <class 'bytearray'>

mv = memoryview(b)                            # Memoryview of bytes (for zero-copy access)
print(mv, type(mv))                           # <memory at ...> <class 'memoryview'>
```

### Special Values

```py
x = None                     # None represents the absence of a value or a null value
print(x, type(x))           # None   # <class 'NoneType'>

# Ellipsis for functions
# NotImplemented for classes

import math               #  Infinity and NaN (from math and float)

inf = float('inf')     # Positive infinity
ninf = float('-inf')   # Negative infinity
nan = float('nan')     # Not a Number

print(inf , type(inf))      #  inf  <class 'float'>
print(ninf, type(ninf))      # -inf  <class 'float'>
print(nan, type(nan))        #  nan  <class 'float'>

print(inf > 1000)      # True
print(ninf < -1000)    # True    
print(nan == nan)      # False (NaN is not equal to anything, even itself)
print(1/0)             # Error

import sys
max_float = sys.float_info.max               # js: Number.MAX_VALUE → largest float in Python
min_float = sys.float_info.min              # js: Number.MIN_VALUE → smallest positive normalized float in Python
```


###  Operations

1. Arithmetic Operations: + - * / %
2. Comparison Operations: == != > < >= <=
3. Logical Operations: and or not
4. Assignment Operations: = += -= *= /= %=
5. Bitwise Operations: & | ^ ~ << >>
6. Identity Operations: is is not
7. Membership Operations: in not in

```python
remainder = a % b          # 10 % 5 = 0
exponentiation = a ** b    # 10 ** 5 = 100000
# Assignment operators
x += 5      # x = x + 5       # Addition assignment (+=) 
x -= 5      # x = x - 5       # Subtraction assignment (-=)
x *= 5      # x = x * 5        # Multiplication assignment (*=)
x /= 5      # x = x / 5       # Division assignment (/=)
x %= 5      # x = x % 5      # Modulo assignment (%=)
```


### Bitwise operators

```py
a = 6       # 110
b = 3       # 011

a & b       # 2 → 110 & 011 = 010      # "and" is a logical operation
a | b       # 7 → 110 | 011 = 111      # "or" is a logical operation
a ^ b       # 5 → 110 ^ 011 = 101
~a          # -7 → bitwise NOT (inverts bits + adds sign)
a << 1      # 12 → shift left by 1: 110 → 1100
a >> 1      # 3 → shift right by 1: 110 → 011
```


####  Indentation

+ Use 4 spaces or 1 tab per indent (PEP 8)
+ Do not mix tabs and spaces
+ You can break long lines with \ or inside () [] {} — next line must be indented

```py
a = 10; b = 20; c = 30     # semicolon separates statements
def greet():
    print("Hello")   # 4 spaces indented

total = (1 + 2 + 3 +
         4 + 5)                   # same as (1 + 2 + 3 + 4 + 5)
```


### print 
```py
# input() and print() are global functions
a = 10
print(a,32, sep="---", end=";")                                  # Output: 10---32;
print(f"my name is {name} and I am {age} years old.")             # f-strings     # recommend
print("my name is {} and I am {} years old.".format(name, age))   # .format() method with empty {}
print("my name is %s and I am %d years old." % (name, age))       # % operator for ordered plcaeholder
```

### input

```py
name = input("Enter your name: ")                                # input just accepts one argument
age = int(input("Enter your age: "))                            # converting type
age: int = int(input("Age: "))                                   # input with type hint
value = input("Enter something (default=hello): ") or "hello"   # default for input    # or is logical operator
```


### Type casting

##### 0. type checking

```py
# we can use int(), float(), str(), ... for type checking
type(123) == int                  # True → check exact type
type("abc") == str                # True
is_int = isinstance(123, int)      # True
is_str = isinstance("abc", str)    # True
```

##### 1. primitive data types
```bash
# int(), str(), bool(), float(), list(), tuple(), set(), dict() are global functions
b = int(3.99)           # Convert float to int (truncates) → 3
# b = int("3.14")       # Error! Cannot convert float-string directly to int
d = float(10)           # Convert int to float → 10.0
g = str(True)           # Convert bool to string → 'True'
j = bool("")            # Empty string to False → False
l = bool([])            # Empty list to False → False
```


##### 2. Non-primitive data types

```py
# To List
n = list("hello")       # Convert string to list of chars → ['h','e','l','l','o']
o = list(123)           # Error! 'int' object is not iterable    # int, complex, float, bool, NoneType are not iterable
o = list((1,2,3))       # Convert tuple to list → [1, 2, 3]
p = list({1, 2, 3})     # Convert set to list → [1, 2, 3]

# To Tuple
q = tuple([1,2,3])      # Convert list to tuple → (1, 2, 3)
r = tuple("abc")        # Convert string to tuple → ('a', 'b', 'c')

# To Set
s = set([1, 2, 2, 3])   # Convert list to set (remove duplicates) → {1, 2, 3}
t = set("hello")        # Convert string to set → {'h', 'e', 'l', 'o'}

# To Dictionary 
u = dict([("a", 1), ("b", 2)])  # → {'a': 1, 'b': 2}    #  convert a list of tuples
```

##### 3. Implicit type casting

```py
print("5" + 4)             # error but in js we get "54"
print("5"*3)              # "555" (string repetition is allowed
print(5 + 2.0)             # 7.0   casting to float
print(True + 10)           # 11   casting boolean to int
print(True + True)            # 2
print(False + 5)            # 5   # false is 0
if 0:                     # casting integer into boolean (False)
if 1:                     # casting integer into boolean (True)
if -1:                    # -1 is True
if []:                     # [], {}, "", set(), range(0) are False
```



### Order of operators ?????

```py

```

### String methods
```py
a = "this is a string"                      # define a string
esc = "I don't \n know"                      # \n = new line

# 1. Accessing
b= len(a)                                   # length of string
substring = a[6:11]                         # Slicing a string
reversed_string = a[::-1]                    # Reversing a string
contains = "is" in a                       # Check if "is" is in string → True    # "in" is membership operator

# 2. Methods
uppercase_string = a.upper()                 # lower(), capitalize(), title(), swapcase()
abc.find("is")                            # find substring, returns index or -1 if not found → 2
abc.rfind("is")                            # last occurrence (same as find if only one) → 5
a = a.replace("string", "text")             # Replace substring → 'this is a text'
split_list = a.split()                   # Splitting a string into a list
starts = a.startswith("this")             # Check if starts with "this" → True
count_is = a.count("is")                   # Count occurrences of "is" → 2

# 3. String operations
"abc" + "def"                               # 'abcdef'    # concatenation
"abc" * 3                                   # 'abcabcabc'   # repetition
```

### Number methods

```py
# sum(), max(), min(), abs(), round(), format() are global functions
sum([1,2,3])	# 6   # Sum of iterable
max([1,2,3])	# 3   # Maximum of iterable	
min([1,2,3])	# 1   # Minimum of iterable
abs(-5)	         # 5    #Absolute value	
round(3.1415)	# 3     # Round number	# like int(3.1415) for type casting
round(3.1415, 0)          # 3.0 (round to 0 decimal places)
round(3.1415, 2)          # 3.14 (round to 2 decimal places)
format(3.1415, ".2g")           # '3.1' 
print(f"{3.1415:.2g}")           # '3.1'          # using f-string for similar effect
```

### Math functions

```py
import math                   # mathematical functions defined by the C standard

pi = math.pi                # π → 3.141592...
e  = math.e                 # Euler's number → 2.718...
x = math.sqrt(16)           # Square root → 4.0
x = math.pow(2, 4)          # 2^4 → 16.0
x = math.ceil(3.2)          # Round up → 4
x = math.floor(3.8)         # Round down → 3
x = math.trunc(3.9)         # Truncate to int → 3
```

### Random numbers

```py
import random

x = random.random()         # Random float between 0.0 and 1.0
x = random.randint(1, 10)   # Random int between 1 and 10 (inclusive)
x = random.uniform(1, 10)    # Random float between 1 and 10
x = random.choice([1, 2, 3]) # Random choice from list
```

### Execute methods

```py
# eval() and exec() are global functions
eval("print(2+2)")              # 4       # Evaluate string as python code
exec("print(2+2)")              # 4         # Execute Python code string
```

### Boolean methods

```py
# bool(), all() and any() are global functions
# True = 1, False = 0
# and, or, not are logical operators
all([1, 2, True])	     # All elements True?	
any([1, 2, True])	     # Any element True?	
bool("string")           # True      # type casting

# boolean operators
True + True              # 2      # type casting implicitly
False + True             # 1
True * 3                # 3
True - 1                # 0
```


###  List methods
```python
# 1. List functions
# len(), sorted(), reversed() are global functions
len([1, 2, 3])                  #  3       # length of list
sorted([3, 1, 2])          # [1, 2, 3]      # gets an iterable and return sorted list	
reversed("abc")	               # <reversed object at 0x0000020116DA9630>     # Return reversed iterator	
reversed([1, 2, 3])        # <list_reverseiterator object at 0x0000023920FD95D0>
list(reversed("abc"))           # ['c', 'b', 'a']    # convert iterator to list

# 2. List methods
dogs = ["Bulldog", "Beagle", "Labrador"]    #  <class 'list'>
dogs.append("Chihuahua")   # Add to the end: ["Bulldog", "Beagle", "Labrador", "Chihuahua"]
dogs.insert(1, "Poodle")   # Insert at index 1: ["Bulldog", "Poodle", "Beagle", "Labrador"]
dogs.pop()                 # Remove last element: "Labrador"
dogs.pop(1)                # Remove element at index 1: "Beagle"
dogs.remove("Poodle")      # Remove by value: "Poodle"
del dogs[0]                # Remove by index: "Bulldog"
dogs.clear()               # Remove all elements: []
dogs.sort()                     # Sort list alphabetically
dogs.reverse()                  # Reverse the list order
copied_dogs = dogs.copy()       # Create a shallow copy of dogs

# String Conversion and joining
", ".join(dogs)            # Convert to string: "Bulldog, Beagle, Labrador"
" * ".join(dogs)           # Join with separator: "Bulldog * Beagle * Labrador"

# Accessing Elements
dogs = ["Bulldog", "Beagle", "Labrador", "Poodle", "Boxer"]
dogs[0]                    # Access first element: "Bulldog"
dogs[-1]                   # Access last element: "Boxer"
dogs[1:3]                  # Slice elements 1 to 3: ["Beagle", "Labrador"]
dogs[:2]                   # First two elements: ["Bulldog", "Beagle"]
dogs[2:]                   # From index 2 onwards: ["Labrador", "Poodle", "Boxer"]
dogs.index("Beagle")            # Find index of "Beagle": 1
"Labrador" in dogs              # Check if exists: True    # membership operators (in, not in) 

# list operations
cats = ["Siamese", "Persian"]
birds = ["Parrot", "Canary"]
animals = dogs + cats + birds    # Combine lists  (concatination)
repeated = [1, 2] * 3             # [1, 2, 1, 2, 1, 2]   (Repetition)
dogs.extend(cats)               # Add cats to dogs
```
####  Array in numpy
```py
import numpy as np
my_array = np.array([1, 2, 3, 4, 5])    # Creating an array
array_sum = np.sum(my_array)  # Sum of elements in the array  # Output: 15
```



#### ES6 functions for lists
```py
# map(), filter() are global functions

# 1. map
numbers = [1, 2, 3, 4]
doubled = map(lambda x: x * 2, numbers)          # return a map object
print(list(doubled))                            # Output: [2, 4, 6, 8]

# 2. filter
even = filter(lambda x: x % 2 == 0, numbers)    # return a filter object
print(list(even))  # Output: [2, 4]

# 3. reduce
from functools import reduce
product = reduce(lambda x, y: x * y, numbers)   # return int
print(product)  # Output: 24
```

###  Tuple
```py
# basic
fruits = ("apple", "banana", "cherry")   #  <class 'tuple'>  # tuple is immutable
len(fruits)              # Get length: 3
single = ("apple",)        # Single-element tuple (comma required)
not_tuple = ("apple")     # Without comma, this is a string
fruits[0] = "orange"     # Error: TypeError, cannot modify tuple elements. # tuple is immutable


# Accessing Elements
fruits[0]                # Access first element: "apple"
fruits[-1]               # Access last element: "cherry"
fruits[1:3]              # Slice elements 1 to 3: ("banana", "cherry")
nested = ((1, 2), (3, 4), (5, 6))
nested[0]                 # Access nested tuple: (1, 2)
nested[0][1]              # Access element in nested tuple: 2

# Operations
veggies = ("carrot", "broccoli")
combo = fruits + veggies  # Combine tuples: ("apple", "banana", "cherry", "carrot", "broccoli")
repeated = fruits * 2    # Repeat elements: ("apple", "banana", "cherry", "apple", "banana", "cherry")

# tuple methods
"apple" in fruits         # Check membership: True
fruits.index("banana")    # Get index of "banana": 1
fruits.count("cherry")    # Count occurrences of "cherry": 1

# unpacking
colors = ("red", "green", "blue")
r, g, b = colors          # Unpacking: r = "red", g = "green", b = "blue"
color1, *others = colors  # color1 = "red", others = ["green", "blue"]    # Partial unpacking

# Sorting
min(fruits)               # Get smallest element (alphabetical)
max(fruits)               # Get largest element (alphabetical)
sorted(fruits)            # Return a sorted list of tuple elements
```

###  Set
```py
fruits = {"apple", "banana", "cherry", "apple"}    # <class 'set'> # unordered collection of unique elements  # {"apple", "banana", "cherry"}
len(fruits)                # Get number of elements: 3
"apple" in fruits          # Check membership: True

# methods
fruits.add("orange")       # Add a single element: {"apple", "banana", "cherry", "orange"}
fruits.update(["mango", "grape"])  # Add multiple elements: {"apple", "banana", "cherry", "orange", "mango", "grape"}
fruits.remove("banana")    # Remove specific element (raises KeyError if not found)
fruits.discard("pear")     # Remove element without error if not found
fruits.pop()               # Remove and return an arbitrary element
fruits.clear()             # Remove all elements: set()
# we cannot directly replace an element in a set 
```
### Frozen Set

```py
frozen = frozenset([1, 2, 3])   # Immutable set     # frozenset is a global function
frozen.add(4)                  # Error: AttributeError (frozensets are immutable)
```

##### Set Operations

```py
# Define two sets
A = {1, 2, 3}
B = {3, 4, 5}

# Union (elements in A or B)
A | B                     # {1, 2, 3, 4, 5}
A.union(B)                # {1, 2, 3, 4, 5}

# Intersection (elements in both A and B)
A & B                     # {3}
A.intersection(B)         # {3}

# Difference (elements in A but not in B)
A - B                     # {1, 2}         # for + we get error
A.difference(B)           # {1, 2}

# Symmetric Difference (elements in A or B, but not both)
A ^ B                     # {1, 2, 4, 5}
A.symmetric_difference(B) # {1, 2, 4, 5}
C = {1, 2}

C.issubset(A)             # Check if C is a subset of A: True
A.issuperset(C)           # Check if A is a superset of C: True
A.isdisjoint(B)           # Check if A and B are disjoint: False
```

####  Dictionary

```py
person = {"name": "Alice", "age": 30, "city": "New York"}    # <class 'dict'>
len(person)              # Get number of key-value pairs: 3
squares = {x: x**2 for x in range(5)}  # Create a dictionary: {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}  # dict comprehension

# Accessing Elements
person["name"]           # Access value by key: "Alice"    # we can not access value by index or using dot notation
person.get("age")        # Get value with a default: 30
person.get("gender", "N/A")  # Default if key not found: "N/A"
person["job"] = "Engineer"  # Add a new key-value pair  or update an existing

# methods
person.pop("city")         # Remove a key and return its value: "New York"
del person["age"]          # Remove a key-value pair
person.clear()             # Remove all items: {}
person.keys()              # Get all keys: dict_keys(["name", "age", "job"])
person.values()            # Get all values: dict_values(["Alice", 31, "Engineer"])
person.items()             # Get all key-value pairs: dict_items([("name", "Alice"), ("age", 31), ("job", "Engineer")])
person_copy = person.copy() # Shallow copy
```


###  enum

```py
from enum import Enum  # enum module 
class Color(Enum):  # enum is a base class
    RED = 1         # enum is a  set of named, constant values
    GREEN = 2       # group of constants
    BLUE = 3         # enum is iterable
# enumerate() is a built-in function, it is something else
favorite_color = Color.GREEN       # output: Color.GREEN   # we access by name
favorite_color = Color(2)          # output: Color.GREEN   # we access by value
```


###  packing and unpacking

```py
# 1. Tuple Packing
x = 1, 2, 3
print(x)      # (1, 2, 3)

# 2. Tuple unpacking
a, b, c = (100, 200, 300)
a, b, c = (100, 200, 300, 400)     # Error
print(a)                        # 100

# 4. unpack a tuple
a= (1,2,3,4)
print(*a)           # 1 2 3 4  # equivalent to print(1, 2, 3, 4)
print(*(1,2,3,4))   # 1 2 3 4

# 3. unpack a list
a= [1,2,3,4]
b= [4,5]
print(*a)            # 1 2 3 4  # equivalent to print(1, 2, 3, 4)
print([33, *b, *a])    # unpack a list   # [33, 4, 5, 1, 2, 3, 4]

# unpack a set
a= {1,2,3}
print(*a)
print([4,*a])

# 2. unpack dictionary
data = {"name": 1, "age": 2, "country": 3}
print(*data)         # unpack all the keys # name age country
print(**data)          # error   # print() function does not accept keyword arguments
myfunc(*data)         # unpack all the keys
myfunc(**data)         # unpack all the values
```

### Comprehension

+ `Comprehension` is a dynamic way to create new collections from iterables
+ basic syntax: `[expr for item in iterable if condition]` --> expr + for + if

```py
a = [x**2 for x in range(5)]                    # [0, 1, 4, 9, 16]                        # 1. List Comprehension
a = {x**2 for x in range(5)}                    # {0, 1, 4, 9, 16}                         # 2. Set Comprehension
a = {x: x**2 for x in range(5)}                 # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}           # 3. Dictionary Comprehension
g = (x**2 for x in range(5))                    # <generator object <genexpr> at 0x000001>  # 4. Generator Comprehension
a = list(g)                                     # [0, 1, 4, 9, 16]
evens = [x for x in range(10) if x % 2 == 0]    # [0, 2, 4, 6, 8]                            # 5. Comprehension with if
a = [[x for x in range(3)] for y in range(3)]   # [[0, 1, 2], [0, 1, 2], [0, 1, 2]]           # 6. Nested Comprehensions
```


### Date and Time

```py
from datetime import date, time, datetime, timedelta

today = date.today()              # 2025-07-09    
today = datetime.today()          # 2025-07-09 11:54:07.409110    
now = datetime.now()              # 2025-07-09 13:10:11.773849   
d = date(2025, 7, 7)              # 2025-07-07
t = time(14, 30, 5)               # 14:30:05
dt = datetime(2025, 7, 7, 14, 30, 45)        # 2025-07-07 14:30:45
dt.year       # 2025            # access items
dt.month      # 7
dt.strftime("%Y-%m-%d")        # '2025-07-07'
dt.strftime("%A, %B %d")       # 'Monday, July 07'
dt.strftime("%I:%M %p")        # '02:30 PM'


import time
timestamp = time.time()         # timestamp
```

### Regex

```py
import re                     # Python's built-in regular expression module
# regex functions
a = re.findall(r'\d+', 'abc123')   # Find all matches in a list
a = re.finditer(r'\d+', 'abc123')   # Find all matches in  an iterator of match objects
a = re.match(r'\d+', 'abc123')      #  Match from start
a = re.sub(r'\d+', '456', 'abc123')        # abc456     #  replace all matches
a = re.search(r'\d+', 'abc123')   #  First match anywhere

# Match Object Methods
print(a)                   # <re.Match object; span=(3, 6), match='123'>
a.group()      # '123'        # get matched string
a.start()      # 3            # index where match starts
a.end()        # 6            # index where match ends
a.span()       # (3, 6)       # tuple of start and end
```

### Memory Behavior
+ In Python, variables don’t have an associated type or size
+ In Python, everything is an object

##### 1. Identity vs Equality

```py
x = [1, 2]              # is (Identity)  -->  checks memory addresses (object identities)
y = [1, 2]              # == (Equality)  -->  checks values
print(id(x))       # 140727710535424           # id() is a global function that returns the memory address (identity) of the object.
print(x == y)       # True (same values)
print(x is y)         # False (different objects)
print(id(x)==id(y))    # False (different objects)
```


###### 2. Interning

+ `Interning` is an optimization technique to reuse small immutable objects in memory instead of creating new ones across the program
+ When an object is interned, any other object with the same value will reference the same memory location
+ interned objects are pre-allocated and cached
+ In CPython, integers in the range `-5 to 256` are interned
+ small strings are interned in python, large strings are not
+ `sys.intern()` function makes sure that identical strings share the same memory location, even if not interned by Python

```py
a = 256              # interned
b = 256
print(a == b)       # True (same value)
print(a is b)      # True (cached)
print(id(a)==id(b))    # True

c = 257               # not interned
d = 257
print(a == b)       # True (same value)
print(c is d)       # False (not cached)
print(id(c)==id(d))    # False
```

##### 3. Assignment

+ All objects (mutable or immutable) are stored in `heap memory`.
+ Variable names are just references (pointers) stored in `stack frames`

```py
x = 545                     # for all data types the result is the same
y = x                     #  Creates new reference(name) to same existing object
print(x == y)             # True    #  after y = x, both x and y will point to the same object 545
print(x is y)             # True    
print(id(x) == id(y))      # True   #  Both variables point to same memory
```

##### 4. Reassignment (Change value after assignment) 

+ Assignment (x=y) for both mutables and immutables makes them reference to the same object
+ modification of mutables `changes the original object` in memory while modification of immutables `creates a new object`

```py
# 1. Mutable: list, dict, set, bytearray
a = [1, 2, 3]  
b = a          #   Both variables point to same memory   (shared reference)
b.append(4)    #  alter the object in memory
print(a, b)       # [1, 2, 3, 4] (both a and b point to same list)
print(a == b)  # True
print(a is b)  # True
print(id(a) == id(b))  # True

# 2. Immutable: int, float, bool, str, tuple
x = 5
y = x  
y += 1    # When an immutable object is "modified" a new object is created in memory, and y refers to that object
print(x, y)  # 5, 6      # The original object remains unchanged
print(x == y)  # False
```


##### 5. Tuples with mutable elements

```py
t = ([1, 2], 3)         # we can modify inner list
t[0].append(3)           # tuple structure remains the same
```

##### 6. Shallow copy and Deep copy

+ both create new objects with duplicating them
+ the purpose is to create `independent duplicates` of objects to aviod side effects of `Shared Reference`
+ `Shallow copy` of a collection is a copy of the collection structure, not the elements.
+ `Deep copy` duplicate everything (structure + elements)
+ Shallow copy is quicker

##### 6.1. List (copy module)

```py
import copy                 # copy.copy() and copy.deepcopy() both create new objects

original = [1,2,3]

default_python = original
copy_method = original.copy()
shallow = copy.copy(original)
deep = copy.deepcopy(original)

default_python[0] = 99        # original = [99, 2, 3]
copy_method[0] = 99           # original = [1, 2, 3]
shallow[0] = 99               # original = [1, 2, 3]
deep[0] = 99                   # original = [1, 2, 3]

print(original)  
```

##### 6.2. Nested List (copy module)
```py
import copy                    # copy.copy() and copy.deepcopy() both create new objects

original = [[1, 2], [3, 4]]

default_python = original            #
copy_method = original.copy()          #
shallow = copy.copy(original)         # Copies references of the inner (nested) objects
deep = copy.deepcopy(original)        # deepcopy creates a new object

default_python[0][0] = 99        # original = [[99, 2], [3, 4]]
copy_method[0][0] = 99           # original = [[99, 2], [3, 4]]
shallow[0][0] = 99               # original = [[99, 2], [3, 4]]      # same as default behavior
deep[0][0] = 99                   # original = [[1, 2], [3, 4]]

print(original)  
```

###  Control Flow

+ Python provides several control flow statements. 
+ These include `if-else`, `for loops`, and `while loops`.
+ Python does not have `goto statement` or `labled statement` or `switch-case`

##### 1. if-else
```py
a= 10
if a>5:
    print("a > 5")
elif a==5:
    print("a == 5")
else:
    print("a < 5")
```

####  2. ternary operator

+ python does not have any special operator for `ternary operator` like js: `condition ? a : b`
+ basic syntax for python: `a if condition else b`

```py
# 1. basic ternary
res = "Positive" if a>0  else "Negative"                          # a > 0 ? "Positive" : "Negative"
res =  "Positive" if x > 0 else "Negative" if x < 0 else "Zero"   # x > 0 ? "Positive" : (x < 0 ? "Negative" : "Zero")   # nested ternary
print("a is greater" if a > b else "b is greater")                 # ternary operator inside print
max = (lambda x, y: x if x > y else y)(4,5)                      # ternary operator inside lambda

# 2. ternary with logical operators  (lazy evaluation)
a > 5 and print("a > 5") or print("a < 5")

# 3. ternary with data types
res = ("Fail", "Pass")[score >= 50]                # (value_if_false, value_if_true)[condition]
res = ("Odd", "Even")[n % 2 == 0]                  # n % 2 == 0 ? "Odd" : "Even"   # ternary operator using tupple (0=false,1=true)
res = {True: "Odd", False: "Even"}[n % 2 == 0]     # n % 2 == 0 ? "Odd" : "Even"   # ternary operator using dictionary
```

#### 2.2 ternary lazy evaluation

+ `Lazy evaluation` means deferring computation until it’s actually needed.
+ Python uses `short-circuit evaluation`: 
    + A and B --->	B is only evaluated if A is True
    + A or B  --->	B is only evaluated if A is False
+ lazy tools: 1. generators 2. ternary 3. itertools library
+ Built-in Lazy functions: 1. range() 2. zip() 3. enumerate() 4. map() 5. filter() 6. iter()

```py
def expensive():
    print("Called")
    return True

print(False and expensive())  # "Called" is NOT printed
print(True or expensive())    # "Called" is NOT printed
print(True and expensive())   # "Called" IS printed
```


####  3. for loop
```py
# 1. Range
for i in range(5):     # range is a function
    print(i)    # Output: 0 1 2 3 4
for i in range(2, 5):          # with range() we do not need to subtract the iterator
    print(i)    # Output: 2 3 4

# 2. List, Set, Tupple, String
for item in [1, 2, 3, 4, 5]:   # iterable
   print(item)                  # 1, 2, 3, 4, 5

# 3. dict
for k in {"name":"ali","age":45}:
    print(k)                                     # age, name
for k,v in {"name":"ali","age":45}.items():      # dict.items() returns a list of tuple (key, value)
    print(k,v)

# 4. enumerate
for i, j in enumerate(['a', 'b', 'c']):       # enumerate() returns a list of tuple (index,value)   [(0, 'a'), (1, 'b'), (2, 'c')]
    print (i, j)     # i is index             # enumerate() is a global function

# 5. zip
fruits = ["apple", "banana", "cherry"]       # zip combines two or more iterables items into tuples
colors = ["red", "yellow", "green"]          # the output is a list of tupples
a = zip(fruits, colors)                      # output: <zip object at 0x000001>
a = list(zip(fruits, colors))               # output: [('apple', 'red'), ('banana', 'yellow'), ('cherry', 'green')]
for fruit, color in zip(fruits, colors):    # zip is for creating a list of tuples
	print(fruit, "is", color)               # zip() is a global function
```

#### 4. While loop

```python
a=10
while a>5:
    print(a)
    a= a-1    # pyton does not support a-- or a++ syntax
```
#### 5. For-else  | while-else

```py
for i in range(10, 0, -1):  # reverse
    print(i)    # Output: 10 9 8 7 6 5 4 3 2 1
else:     # for-else executed because no break in for
    print("Finally finished!")
```

#### 6. Break, continue

```py
for i in range(5):
    if i == 3:
        break               # stop and break and exit the loop
    print(i)                 # 0 1 2

for i in range(5):
    if i == 3:             # Skips the rest of the loop body for current iteration
        continue          # Skips current iteration, continues loop
    print(i)              # 0 1 2 4 
```

#### 7. Pass

```py
for i in range(0, 10, 2):    # range(start, stop, step)
    pass         # pass statement for empty loops or functions that don't need to do anything
for i in [1,2,3,4]:
    print(i)
    pass         # pass statement not executed (diff with break)
    print("Hello")
```


### functions


#### parameter vs argument

```py
def greet(name):    # ← name is a parameter     ---> define
    print("Hello", name)

greet("Alice")      # ← "Alice" is an argument  ---> call
```

#### defult values
```py
def func(a=10, b=20, c=30):
    print(a, b, c)

func()           # the only way that number of arguments can be less than required
func(1)          # a=1
func(1,2,3,4)    # error
```

#### Calling functions

```py
def order_food(side, drink, main="Pizza"):                    # default values should be at the end otherwise we get error
    print(f"Main: {main}, Side: {side}, Drink: {drink}")
    
order_food("Burger", "Fries", "Coke")                  # Positional calling (exact order and exact number of arguments(Required))
order_food(main="Burger", side="Fries", drink="Coke")  # Keyword calling (we can change the order)
order_food("Fries","Coke")              # keyword calling:  explicitly naming the parameters, instead of relying on their position.
order_food("s",side="ds")            # Error: got multiple values for argument 'side'
order_food(side="ds","s")            # Error:  positional argument follows keyword argument
```

#### packing arguments

```py
def myfunc(name, age, country):
    print(name, age, country)

mydict = {"name": "ali", "age": 30, "country": "India"}
mylist = [1 ,2 ,3]
myfunc(*mylist)             # 1 2 3
myfunc(*mydict)             # name age country
myfunc(**mydict)             # ali 30 India
```

#### *args and **kwargs
+ *args and **kwargs are called `Variable-Length Arguments`  or `Arbitrary Arguments`

```py
# 0. args and kwargs
def pack_args(*args, **kwargs):     # args is tuple and kwargs is dict
    print(args, kwargs)            # () {}

pack_args()

# 1. args
def pack_args(*args):     # args packs any number of positional args into a tuple
    print(args, type(args))    # 1,2,3  <class tuple>

pack_args(1, 2, 3)         # this function called 'variadic function'

# 2. kwargs
def pack_args(**kwargs):      # kwargs packs named args into a dict
    print(kwargs, type(kwargs))    # {'name': 'Ali', 'age': 30} <class 'dict'>

pack_args(name="Ali", age=30)

# 3. args and kwargs
def pack_args(*args,**kwargs):        # args and kwargs are used for packing function arguments
    print(kwargs, type(kwargs))        # kwargs for named args
    print(args, type(args))              # args for unnamed args(positional)

pack_args(45, 44, name="Ali", age=30)    # unnamed args should become first

# 4. args, kwargs, normal arguments
def pack_args(a, b=10, *args, name="default", age=10, **kwargs):
    print(args)                    # normal args should be before *args
    print(kwargs)                  # normal kwargs should be before **kwargs
    print(a, b)                     # *args and **kwargs do not have default values --> () {}
    print(name, age)

pack_args(1,2)                                  # a=1, b=2
pack_args(1,2,3,4)                                # a=1, b=10, args=(3, 4), name='default', age=10, kwargs={}
pack_args(1,2,3,4,5,6,7)                                # a=1, b=10, args=(3,4,5,6,7), name='default', age=10, kwargs={}
pack_args(1,2,3,4, year=30, name="Ali")             # a=1, b=2, args=(3, 4), name='Ali', age=10, kwargs={'year': 30}
```


#### 0. Special parameters (Keyword-Only Arguments)
```py
def func(a, b, *, option=True):  # Everything after `*` must be a keyword argument
    pass

func(1, 2, option=False)  # Valid
func(1, 2, False)         # Error (must use `option=False`)
```

#### 0. Special parameters (Positional-Only Arguments)
```py
def func(a, b, /, c):  # `a` and `b` must be positional
    pass

func(1, 2, 3)      # Valid
func(1, b=2, c=3)  # Error (`b` cannot be keyword)
```


#### calling values
+ Python’s behavior is neither exactly “call by value” nor “call by reference”
+ it's often described as: `Call by Object Reference` or `Call by Assignment`

###### 1. Call by Value

```c
void foo(int x) {          // C-like behavior
    x = 5;
}
int a = 3;
foo(a);  // a is still 3
```

###### 2. Call by Reference

```c
void foo(int &x) {    // A reference (address) to the original variable is passed.
    x = 5;
}
int a = 3;
foo(a);  // a becomes 5        // Changes affect the original variable.
```

###### 3. Call by Object Reference

+ If the object is mutable, changes inside the function can affect the original.
+ If the object is immutable, reassigning inside the function does not affect the original.

```py
# 1. mutable
def add_item(lst):
    lst.append(4)

my_list = [1, 2, 3]           # list, dict, set, bytearray are mutable
add_item(my_list)
print(my_list)  # Output: [1, 2, 3, 4]

# 2. immutable
def set_to_five(x):
    x = 5

a = 10                       # int, float, str, NoneType, bool, bytes, tuple, frozenset are immutable
set_to_five(a)
print(a)  # Output: 10
```

###### 4. Prevent Call-by-Reference Behavior

```py
my_func(my_list[:])
my_func(list(my_list))
my_func(my_list.copy())
my_func(my_dict.copy())
my_func(my_set.copy())
# or use copy.deepcopy()
```


####  5. return multiple values
```py
def stats(numbers):
    return max(numbers), min(numbers), sum(numbers) / len(numbers)

a = stats([1, 2, 3])             # a is a tuple
print(a)                           # Output: (3, 1, 2.0)   #  return a tuple
maximum, minimum, average = stats([1, 2, 3, 4, 5])     # unpack a tuple
print(maximum, minimum, average)  # Output: 5 1 3.0
```

#### 2. Ellipsis 

```py
def sum(a,b):
    pass         # do nothing just pass

def func():           # Ellipsis is often used as a placeholder for code to be implemented in future
    ...               # Ellipsis is a punctuation mark consisting of a series of three dots

print(...)         # Output: Ellipsis
print(type(...))   # <class 'ellipsis'>
```

####  3. Docstring
```py
def greet(name):
    """ A description about this function"""
    return f"Hello, {name}!"

print(greet.__doc__)  
print(greet.__globals__)                   # list of all dunder methods with their values
```

####  4. Function Annotations
```py
def greet(name: str="default value") -> str:    # int, float, str, bool, list, tuple, dict, set
    return f"Hello, {name}!"

print(greet("Alice"))                        # Output: Hello, Alice!
print(greet.__annotations__)                 # {'name': <class 'str'>, 'return': <class 'str'>}

# Variable Annotations
x: int = 10
y: str = "hello"
print(__annotations__)          # {'x': <class 'int'>, 'y': <class 'str'>}  # Works in module scope
```


#### 1. Lambda (Anonymous) Functions
```py
# useage: use them as an anonymous function inside another function
add = lambda x, y: x + y             # save lambda as a variable
print(add(2, 3))  # Output: 5

a = (lambda x:x+1)(2)            # call immediately the lambda function
print(a)                        # 3
```



####  6. global variables
```py
x = 10  # Global
def modify_var():        # we cannot have both a global and local variable with the same name in the function
    global x               # Use global keyword to modify
    x = 20               # change the global variable

modify_var()
print(x)  # Output: 20
```

#### Nested function

```py
def outer_function():
    a = 1
    def inner_function():                 #  inner_function is a nested function
        print(a)                           # inner_function accesses outer_function variables
    inner_function()                       # Called inside outer

outer_function()
inner_function()                   # Error
print(outer_function.__closure__)         # None
```

####  7. clouser

+ `closure` is created when a nested function `remembers` and uses variables from its enclosing function — even after the outer function has finished executing.

```py
def outer_function(text):    
    a = 1
    def inner_function():
        print(text, a)            # nested function access to outer variables
    return inner_function          # instead of calling we should return the inner function

closure_function = outer_function("Hello")   # outer function retains variables since they will used in inner function
closure_function()  # Output: Hello, 1
```


#### 8. decorator

+ `Decorator` is a function that `modifies` the behavior of another function or method — without changing its code directly.
+ `Decorator` is a function that takes another function as input and returns a new function with extended or altered behavior
+ Imagine a decorator as a wrapper around a gift
+ we can create decorators in two ways: 1. function based decorator 2. class based decorator
+ use cases: 1. logging 2. timing 3. caching

```py
def mydecorator(func):                     # decorator takes another function as input
    print(func)                             # <function say_hello at 0x0000022D1BEA9260>
    def wrapper(*args, **kwargs):           
        print(args, kwargs)                   # (1, 2) {'name': 'default'}
        print("logs before function call")  # code before
        result = func(*args, **kwargs)       # pass args and kwargs
        print("logs after function call")  # code after
        return result
    return wrapper                  # we need to return the wrapper

@mydecorator                     # Apply Decorator to this function 
def say_hello(a,b, name="default"):
    print("Hello!")
# mydecorator will execute with no need to call say_hello like this: `say_hello()`
```

#### decorator with arguments

```py
def repeat(n):                # for argument we should add one more function
    print(n)                 # 3
    def decorator(func):
        print(func)           # <function greet at 0x0000022D1BEA9260>
        def wrapper(*args, **kwargs):
            for _ in range(n):
                func(*args, **kwargs)
        return wrapper
    return decorator

@repeat(3)                         # execute this function 3 times
def greet():
    print("Hi!")
```

#### 9. Factory function

+ `factory function` is a function that creates and returns objects
+ same as a real-world factory producing products.
+ Often used when you don’t want to directly call the class constructor
+ useful when we have complex logic, and we have to create multiple objects again and again that have the same logic
+ Factory functions are a form of design pattern
+ In class-based programming, a factory is an abstraction of a constructor of a class

```py
class Dog:
    def speak(self):
        return "Woof!"

class Cat:
    def speak(self):
        return "Meow!"

def animal_factory(kind):         # factory function
    if kind == "dog":             # we have a complex logic for creating objects
        return Dog()             # 	factory function hides which class is being instantiated ---> abstraction
    elif kind == "cat":
        return Cat()              # return a new object
    else:
        raise ValueError("Unknown animal type")

pet = animal_factory("cat")                # pet is an object of a class
print(pet.speak())  # Meow!
```

#### 10. Higher-order Function

+ `Higher-Order Function` is a function that takes a function as an argument or returns a function as a result — or both.
+ common built-in higher-order functions: 1. map, 2. filter, 3. reduce

##### 10.1. take function as argument
```py
def shout(text):
    return text.upper()

def whisper(text):
    return text.lower()

def speak(func, message):      # takes a function as an argument
    return func(message)        # returns a function as a result

print(speak(shout, "hello"))   # Output: HELLO
print(speak(whisper, "HELLO")) # Output: hello
```

#### 10.2 return a function

```py
def sum():                    # this is a factory function
    def add(x,y):
        return x+y
    return add               # return a function
```

#### 11. Generators (Function with yield)

+ useage: 1. Nested structures 2. Reading Large Files 3. infinite sequences 4. streams

```py
def count_up_to(max):             # generator function creates and returns an iterator so it can be used in for loops
    count = 1                    #  any function that uses yield is a generator function.
    while count <= max:
        yield count           # unlike return, yield pauses the function
        count += 1             # generators can have multiple yield statements

counter = count_up_to(2)        # counter is an iterator      #  Does not execute yet
print(counter)                   # <generator object count_up_to at 0x0000022D1BEA92E0>
print(next(counter))  # 1       # next() is used to get the next value from the iterator
print(next(counter))  # 2
print(next(counter))  # Error: StopIteration 

for num in count_up_to(5):          # iterate over the iterator
    print(num)                      # 1 2 3 4 5
```



#### 12. Memoization (Caching)

+ Memoization is a technique to cache function results
+ if the same inputs occur again, the function can return the stored result instead of recalculating it
+  

```py

```

###  try-except

```py
try:               # useage: 1. user input 2. open a file 3. db connection
    x = 10 / 0
except ZeroDivisionError as e:
    print("Division by zero is not allowed!", e)
else:
    print("Success")  # Runs if no exception occurs
finally:
    print("Always executed")  # Always executed
```

### File Handling

```py
# 1. Writing to a file
file = open("output.txt", "w")    # Opens a file in a specified mode ('r'= reading, 'w'=writing, 'a' = appending, 'x'= exclusive)
# 'w': Opens the file in write mode (this will create the file if it doesn't exist)
# 'x': Exclusive creation (creates a new file, but raises an error if the file already exists)
file.write("Hello, World!\n")        # Writes a string to the file
file.close()                        # closes the file

# 2. Reading from a file
file = open("output.txt", "r+")       # open is a global function
contents = file.read()    # Reads the contents of a file
print(contents)            # r+ means read and write (r+w)
file.close()

# 3. With statement
with open('input.txt', 'r') as file:     # with statement automatically close the file, even if errors occur
    lines = file.readlines()           # Read all lines and store them in a list
    for line in lines:           # Print the content of the file
        print(line.strip())      # strip() removes the trailing newline character
# File is automatically closed here          # with + as

# 4. With statement
with open('output.txt', 'w') as file:
    a = ["first line", "second line"]
    for line in a:               # Write each line from the list to the file
        file.write(line + '\n')  # Add a newline character at the end of each line
```

###  Command-Line Arguments

```py
import sys               # run this file with:   "script.py arg1 arg2"
arg0 = sys.argv[0]       # name of the script file 
arg1 = sys.argv[1]       # sys.argv is a list that contains command-line arguments
arg2 = sys.argv[2]
print("Argument 1:", arg1, "Argument 2:", arg2)
```

### Python Modules

##### 1. import libraries

```py
import math                        # import entire module
import numpy as np                # import module with alias
from math import sqrt, pi, exp    # import specific functions
from math import *                # import all functions  (not recommended)
```

##### 2. import files

```py
# utils.py
def greet(name):                    # there is no need for export in python
    return f"Hello, {name}!"
# main.py
from utils import greet           # from 'file_name' import 'function_name'
from tools.utils import greet    # from 'folder_name.file_name' import 'function_name'
```

##### 3. group exports

```css
my_project/
│
├── main.py                  /* __init__.py marks a directory as a Python package */
└── tools/                    /* __init__.py  It can be empty or contain Python code, and it's executed when the package is imported */
    ├── __init__.py           /* __init__.py is used to create a package */
    └── helper.py              /* help() is inside this file */
    └── utils.py              /* greet() is inside this file */
```

```py
from tools import *                     #  Wrong: we can not use any function by this way
from tools.helper import help          # help()       # when we import these __init__.py will be executed
from tools import utils              # utils.greet()
```

##### 4. control exports

```py
# utils.py
__all__ = ['foo']           # __all__ is used to define the public interface of a module
def foo():                  # __all__ limits `import *`
    print("foo")
def _hidden():
    print("hidden")

# main.py
from utils import *          # foo() imported but _hidden() not
```

##### 5. Special Variables

+ `__name__` is a `built-in special variable` which is dunder method (magic method)
+ __name__ evaluates to the name of the current module.
+ we use ` __name__ == "__main__"` to run the code only when the script is executed directly, not when imported.

```py
print(__name__, type(__name__))          #  '__main__' if run directly    # <class str>
print(__file__)           # Current file path
print(__doc__)           # current files's docstring (None if not set)
print(__package__)        # package name of a module (folder name)
print(__builtins__)       # <module 'builtins' (built-in)>   
a = __builtins__.len("123")   # __builtins__ is a  dictionary that provides access to Python's built-in functions
```

### os

### pathlib


### Serialization and deserialization


### OOP in python

##### 1. Classes and Objects
+ class is a blueprint or template that defines the properties (attributes) and behaviors (methods) that objects of that class will have. 

```python
class Person:                       # Define a class
    def __init__(self, name, age):   # self represents the instance of the class
        self.name = name
        self.age = age

    def greet(self):
        print(f"Hello, my name is {self.name} and I am {self.age} years old.")

    def __del__(self):                # __init__ is constructor and __del__ is destructor    # usage: closing a file in the class
        print("Cleaning up...")       # Not guaranteed to run immediately when del obj is called

p1 = Person("Ali", 38)                      # Create instance
p2 = Person(age=23, name ="Ahmad")     # keyword calling
print(p1)                     # <__main__.Person object at 0x000001C9B9B9D0A0>
print(type(p1))                 # <class '__main__.Person'>
print(Person)                       # <class '__main__.Person'>
print(type(Person))                 # <class 'type'>
print(p1.name)                   # access attributes   # Ali
p1.greet()                  # call methods
print(p1.__dict__)            # {'name': 'Ali', 'age': 38}        #  Inspecting an Object's Attributes
```


####  2. Static 

###### 2.1 static property

```py
class MyClass():                                    # using pranteses is optianl
    class_attribute = "I belong to the class!"    # Class attribute (shared by all instances)
    def __init__(self, instance_attribute):        # Constructor
        self.instance_attribute = instance_attribute   # statics do not depend on any object instance

a = Myclass("name")
print(a.class_attribute)                       # I belong to the calss
print(Myclass.class_attribute)                 # I belong to the class
```

###### 2.2 static method 

```py
class MyClass:
    class_attribute = "I belong to the class"                  # static property
    def __init__(self, instance_attribute):                    # self refers to instance
        self.instance_attribute = instance_attribute          # instance_attribute

    def instance_method(self):                                # instance_method have access to self
        return f"This is an instance method. {self.instance_attribute}"

    # @classmethod and @staticmethod are decorator
    @classmethod                     #  methods requiring the class itself
    def class_method(cls):            #  class method do not have access to self but access to cls
        return f"This is a class method. {cls.class_attribute}"  # cls refers to class (not instance)

    @staticmethod          # for utility methods
    def static_method():   # static method have access neither to cls nor self 
        return "This is a static method."  # in any language static method do not have access to self (this)

print(Myclass.static_method())  # Output: This is a static method.
print(MyClass.class_method())   # Output: This is a class method. I belong to the class!
# static method usage: 	Utility/helper functions                
# class method usage: Factory methods, class behavior
```

###### 2.3 class Docstring

```py
class Myclass:
    """A simple example class."""     # Class documentation : docstring
    def __init__(self, name):
        self.name = name

print(Myclass.__doc__)              # like static
print(help(Myclass))               # more detailed
```

###### 2.4 class Annotations
```py
class Person:
    name: str          # Class variable annotation                # we cant print this one
    age: int = 0       # Class variable with default value
    
    def __init__(self, name: str, age: int) -> None:
        self.name = name  # Instance variable (type inferred from parameter)
        self.age = age

print(Person.__annotations__)
```

###### 2.5 Inspect Names in a Namespace
```py
class Example:           # dir() lists all attributes/methods, including `x`
    def __init__(self):  
        self.x = 10

print(dir(Example())) # output:   ['__class__', '__dict__', '__dir__', '__doc__', '__eq__' ...] 
```

####  3. Dunder methods in oop
```py
class Person:                         # “Dunder” is short for “double underscore”
    def __init__(self, name, age):   # Dunder methods also called magic methods or special methods
        self.name = name             # Dunder methods allows you to customize the behavior of Python objects
        self.age = age

    def __str__(self):                              # Custom string representation for instance --> should return a string
        return f"{self.name}, {self.age} years old"     # print(Person("ali", 12))      # defult : <__main__.Person object at 0x00>

    def __repr__(self):                # JJJ
        return "repr called"

    def __call__(self, x):           # __call__ allows an instance of a class to be called like a function
        return x * 2

    def __add__(self, other):      # Overload + operator
        return self.age + other.age
    
    def __new__(cls, *args, **kwargs):  # responsible for creating instances before __init__ is called
        print("__new__ called")
        return super().__new__(cls)

    def __del__(self):                 # destructor
        print("__del__ called")

    def __len__(self):                 # customise len() method when len(obj)
    def __getitem__(self):               # Indexing like a list or dict when obj[key]
    def __setitem__(self):              # Assign a key when obj[key]=value
    def __delitem__(self):             # Enables deletion of items with del obj[key] syntax

p1 = Person("Alice", 30)
p2 = Person("Bob", 25)
print(repr(p1))  # Uses __repr__        # repr() is a global function
print(str(p2))   # Uses __str__
print(p1)               # Output: Alice, 30 years old
print(p1 + p2)          # Output: 55
print(p1(10))           # Output: 20             # __call__ like a function
```

### 4. NotImpelemented

```py
class MyClass:                        #  NotImplemented
    def __eq__(self, other):           # __eq__ is for Equality comparison when ob1==obj2
        return NotImplemented          # Used in special methods to indicate unsupported operations
```


####  5. Single Inheritance

```py
class Parent:
    def greet(self):
        return "Hello from Parent"

class Child(Parent):
    def greet(self):
        return "Hello from Child"

child = Child()
print(child.greet())  # Hello from Child
```
####  6. Multiple Inheritance

```py
class A:
    def method_a(self):
        return "A method"

class B:
    def method_b(self):
        return "B method"

class C(A, B):        # C inherits from A and B
    pass              # C is a subclass of A and B

obj = C()               # C has all the methods of A and B
print(obj.method_a())  # Output: A method
print(obj.method_b())  # Output: B method
```

####  5. Super 

```py
class Parent:
    def greet(self):
        return "Hello from Parent"

class Child(Parent):
    def greet(self):
        parent_greet = super().greet()    # call the parent
        return f"{parent_greet}. And hello from Child!"

child = Child()
print(child.greet())  # Output: Hello from Parent. And hello from Child!
```

####  6. Encapsulation

+ `name mangling`: when you define a variable with double underscore in a class, python "mangles" the name internally to `-Classname__variablename`

```py
class EncapsulationExample:   # Python doesn't have true access modifiers like public, protected, and private like Java
# Encapsulation means: bundling data and methods that operate on the data while restricting access
    def __init__(self):                           # these are "Access Modifiers"
        self.public = "I am public!"               # public means accessible from anywhere
        self._protected = "I am protected!"         # protected means accessible within the class and its subclasses
        self.__private = "I am private!"          # private means accessible only within the class

    def get_private(self):                  # get_private() is a public method
        return self.__private                # this is a getter functionn for __private variable
        
    def public_method(self):                # public_method() is a public method
        print("I am public!")

    def _protected_method(self):            # _protected_method() is a protected method
        print("I am protected!")

    def __private_method(self):             # __private_method() is a private method
        print("I am private!")

obj = EncapsulationExample()
print(obj.public)       # Output: I am public!
print(obj._protected)   # Output: I am protected!
print(obj.__private)     # AttributeError: object has no attribute '__private'
print(obj.get_private())  # Output: I am private!
print(obj._EncapsulationExample__private)    # Output: I am private!      # using name mangling

obj.public_method()
obj._protected_method()        # executes but with warning in editor
obj.__private_method()       # AttributeError: object has no attribute '__private_method'
obj._EncapsulationExample__private_method()        #  works via name mangling
```


####  8. Abstraction

+ `Concrete class` is the opposite of an abstract class
+ A concrete class is a fully implemented class that you can create objects from (instantiate)
+ `concrete data type` refers to built-in data types that can hold actual values (unlike abstract types like interfaces or base classes)

```py
from abc import ABC, abstractmethod    # In Python, interfaces are not explicitly defined
# Abstraction means:  Hiding complex implementation details (hides bluprint) and showing only the essential features to the user. like TV remote
# Abstraction often involves defining interfaces or abstract classes (Interface-Based Interaction)
class Animal(ABC):               # Abstract Base Class =ABC  defines a blueprint for other classes
    @abstractmethod              # @abstractmethod means it must be overridden in subclass
    def speak(self):              # ABC class can’t be used directly (can not instantiate)
        pass                     # class Animal is an interface

class Dog(Animal):
    def speak(self):
        return "Woof!"

# dog = Animal()  # Error: Can't instantiate abstract class
dog = Dog()
print(dog.speak())  # Output: Woof!
# Abstraction and encapsulation work together. 
# Encapsulation hides the internal state of an object, while abstraction focuses on exposing only the necessary features.
```


#### 8. Polymorphism 

```py
class Animal:                      # Polymorphism means:   "Same interface, different behavior"
    def speak(self):               # we can define Animal as an interface
        return "Some sound"

class Cow(Animal):
    def speak(self):
        return "Moo"

class Sheep(Animal):
    def speak(self):
        return "Baa"

a = Cow()
b = Sheep()
print(a.speak())
print(b.speak())
```


####  9. Getter and Setter

+ getters and setters are used to `access and modify private members` safely.
+ They are a form of encapsulation since they hide internal representation of data.
+ They validate and provide controlled access to data (encapsulation).
+ Python encourages using `@property` instead of explicit getters/setters for cleaner syntax.

```py
class Person:
    def __init__(self, name, age):
        self.__name = name                # Private attribute (conventionally prefixed with __)
        self.__age = age

    def get_name(self):
        return self.__name

    def set_name(self, name):
        if isinstance(name, str) and len(name) > 0:           # validation
            self.__name = name
        else:
            print("Invalid name. Name must be a non-empty string.")

    def get_age(self):
        return self.__age

    def set_age(self, age):
        if isinstance(age, int) and age >= 0:
            self.__age = age
        else:
            print("Invalid age. Age must be a non-negative integer.")

person = Person("Alice", 30)     # calls constructor and not setter methods
print(person.get_name())  # Output: Alice          # call getter method for accessing private attribute
person.set_name("Bob")               # set new value by setter
print(person.get_name())  # Output: Bob  
person.set_age(-5)       # Output: Invalid age. Age must be a non-negative integer.
print(person.get_age()) # Output: 30
```

####  10. Getter and Setter in pythonic way

```py
class Rectangle:
    def __init__(self, width, height):
        self.__width = width                 # private variable
        self.__height = height

    @property              # @property is a built-in decorator that makes method behave like an attribute
    def area(self):                           # area is a dynamic read-only property (no setter)
        return self.__width * self.__height

    @property                          # Getter for width
    def width(self):                   # we can access width without using the double underscore 
        return self.__width             # we can write a getter for height also

    @width.setter                      # @<name>.setter
    def width(self, value):             # setter for width        # we can define a setter for height also
        if value <= 0:                  # in setter function we should validate the value
            raise ValueError("Width must be positive!")
        self.__width = value

rectangle = Rectangle(10, 5)
print(rectangle.area)  # Output: 50       # area is a property (a method that used @property)
print(rectangle.width)  # Output: 10    # call getter and we accessed without using the property (__width)
rectangle.width = 20  # call setter
print(rectangle.area)  # Output: 100    # area updated via width change
```

#### 10. Class-Based Decorator

```py
class MyDecorator:
    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwargs):  # __call__ executes when we call the object (instance), in this case greet()
        print("Before call")
        result = self.func(*args, **kwargs)
        print("After call")
        return result

@MyDecorator
def greet():
    print("Hello!")
```

#### 11. Operator overlaoding

```py
# --- 1. Math Operators ---
class MyNumber:
    def __init__(self, value):
        self.value = value

    def __add__(self, other):                    # customise + operator
        return self.value + other.value + 100

    def __mul__(self, other):                    # customise * operator
        return self.value * other.value * 100

    def __str__(self):
        return str(self.value)

n1 = MyNumber(2)
n2 = MyNumber(3)
print(n1 + n2)  # __add__
print(n1 * n2)  # __mul__

# --- 2. Comparison Operators ---
class Compare:
    def __init__(self, value):
        self.value = value

    def __eq__(self, other):
        return self.value == other.value

    def __lt__(self, other):
        return self.value < other.value

c1 = Compare(5)
c2 = Compare(10)
print(c1 == c2)  # __eq__
print(c1 < c2)   # __lt__
```

### errors and exceptions











































