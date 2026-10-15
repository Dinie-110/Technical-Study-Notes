# Python Study Notes 🐍

A practical beginner-to-intermediate Python reference with runnable examples. Compatible with Python 3.10+ unless otherwise noted.

## Table of Contents
1. [Introduction](#1-introduction)
2. [Variables and Data Types](#2-variables-and-data-types)
3. [Operators](#3-operators)
4. [Conditions](#4-conditions)
5. [Loops](#5-loops)
6. [Strings](#6-strings)
7. [Collections](#7-collections)
8. [Functions and Scope](#8-functions-and-scope)
9. [Comprehensions](#9-comprehensions)
10. [Exceptions](#10-exceptions)
11. [Files](#11-files)
12. [Object-Oriented Programming](#12-object-oriented-programming)
13. [Modules and Packages](#13-modules-and-packages)
14. [Functional Tools](#14-functional-tools)
15. [Iterators and Generators](#15-iterators-and-generators)
16. [Decorators](#16-decorators)
17. [JSON and CSV](#17-json-and-csv)
18. [Virtual Environments](#18-virtual-environments)
19. [Best Practices](#19-best-practices)
20. [Interview Questions](#20-interview-questions)
21. [Practice Exercises](#21-practice-exercises)

## 1. Introduction
Python is a high-level, general-purpose programming language commonly used for automation, web development, data analysis, AI, and scripting. Python uses indentation to define blocks.

```python
print("Hello, World!")
name = input("Your name: ")
print(f"Hello, {name}!")
```

Run a script with `python main.py` (or `python3 main.py`, depending on your installation). Lines beginning with `#` are comments.

## 2. Variables and Data Types
Variables refer to objects; you do not declare their types in advance.

```python
age = 24                 # int
price = 19.99            # float
name = "Aisha"           # str
is_student = True        # bool
missing = None           # NoneType
numbers = [1, 2, 3]     # list
coordinates = (3, 4)    # tuple
unique = {1, 2, 3}      # set
person = {"name": "Aisha", "age": 24}  # dict
print(type(age))
print(int("12") + 3)      # 15
print(str(42))           # '42'
```

Mutable types include lists, dictionaries, and sets. Immutable types include integers, floats, strings, tuples (although a tuple can contain mutable objects), and booleans. `type(x)` inspects a type; `isinstance(x, int)` checks type membership.

## 3. Operators

| Category | Operators | Example |
|---|---|---|
| Arithmetic | `+ - * / // % **` | `7 // 2 == 3`, `2 ** 3 == 8` |
| Comparison | `== != > < >= <=` | `5 >= 3` |
| Logical | `and or not` | `True and False` |
| Assignment | `= += -= *= /=` | `count += 1` |
| Membership | `in`, `not in` | `'a' in 'cat'` |
| Identity | `is`, `is not` | `value is None` |

`/` produces true division; `//` is floor division (toward negative infinity). `==` compares values; `is` checks whether references point to the same object.

```python
x = 7
print(x / 2)     # 3.5
print(x // 2)    # 3
print(x % 2)     # 1
print(x ** 2)    # 49
print(0 < x < 10)  # True
```

## 4. Conditions
Conditions choose which block executes. Only the first matching `if`/`elif` branch runs.

```python
score = 82
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
else:
    grade = "C"
print(grade)  # B

status = "adult" if score >= 18 else "minor"  # conditional expression
```

Falsy values include `False`, `None`, `0`, `""`, `[]`, `{}`, and `set()`. Other ordinary values are truthy.

## 5. Loops
A `for` loop iterates over elements; a `while` loop repeats while a condition stays true.

```python
for number in range(1, 4):
    print(number)  # 1, 2, 3

count = 0
while count < 3:
    print(count)
    count += 1

for number in range(5):
    if number == 2:
        continue  # skip this iteration
    if number == 4:
        break     # exit loop
    print(number)
```

Use `enumerate(items)` to obtain an index and value; `zip(a, b)` to iterate over corresponding values. A loop's `else` runs only if the loop finishes without `break`.

```python
names = ["Ali", "Sara"]
for index, name in enumerate(names, start=1):
    print(index, name)
```

## 6. Strings
Strings are immutable sequences of Unicode characters. Indices start at 0; slices exclude their end index.

```python
text = "  Python Programming  "
print(text.strip())           # remove outer whitespace
print(text.lower())           # lowercase
print(text.upper())           # uppercase
print(text.replace("Python", "Java"))
print("apple,banana".split(","))
print("-".join(["a", "b"]))
print(text.strip().startswith("Python"))
print("Python"[0])            # P
print("Python"[1:4])          # yth
print("Python"[::-1])         # nohtyP
name, score = "Aisha", 95
print(f"{name} scored {score}%")
```

Common methods: `.find()`, `.count()`, `.isdigit()`, `.isalpha()`, `.startswith()`, `.endswith()`, `.strip()`, `.split()`, `.join()`.

## 7. Collections

### Lists: ordered, mutable, allow duplicates
```python
fruits = ["apple", "banana"]
fruits.append("mango")          # add one item
fruits.extend(["pear", "kiwi"]) # add multiple items
fruits.insert(1, "orange")      # insert at index
fruits.remove("banana")         # remove matching value
last = fruits.pop()             # remove and return last
print(fruits[0], fruits[-1])
print(fruits[:2])
fruits.sort()                   # in-place
sorted_copy = sorted(fruits)    # new list
```

### Tuples: ordered, immutable
```python
point = (10, 20)
x, y = point  # unpacking
single = (5,)  # comma makes it a tuple
```

### Sets: unique elements, no guaranteed order
```python
a = {1, 2, 3}
b = {3, 4}
print(a | b)  # union
print(a & b)  # intersection
print(a - b)  # difference
print(a ^ b)  # symmetric difference
```

### Dictionaries: key-value mappings
```python
student = {"name": "Ali", "age": 21}
student["grade"] = "A"
print(student.get("city", "Unknown"))  # safe default
for key, value in student.items():
    print(key, value)
print(student.keys())
print(student.values())
student.update({"age": 22})
removed = student.pop("grade")
```

Dictionary keys must be hashable (for example, strings or integers; not lists). Since Python 3.7, dictionaries preserve insertion order.

## 8. Functions and Scope
Functions group reusable code. `return` sends a result to the caller; `print` only displays output.

```python
def greet(name: str, greeting: str = "Hello") -> str:
    return f"{greeting}, {name}!"

print(greet("Ali"))
print(greet(name="Sara", greeting="Hi"))

def total(*numbers):
    return sum(numbers)

def show_profile(**details):
    for key, value in details.items():
        print(key, value)

print(total(1, 2, 3))
show_profile(name="Ali", age=21)
```

Local variables exist within functions; `global` and `nonlocal` allow rebinding names in enclosing scopes, but avoid them unless needed.

**Mutable default argument pitfall:**
```python
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

Do not use `items=[]` as a default when you expect a fresh list per call.

## 9. Comprehensions
Compact ways to build collections.

```python
squares = [x * x for x in range(5)]
evens = [x for x in range(10) if x % 2 == 0]
square_map = {x: x * x for x in range(4)}
unique_lengths = {len(word) for word in ["cat", "dog", "horse"]}
print(squares, evens, square_map, unique_lengths)
```

Prefer an ordinary loop if the comprehension becomes difficult to read.

## 10. Exceptions
Use exceptions to handle errors gracefully. Catch specific exceptions rather than hiding all failures.

```python
try:
    value = int("12")
    result = 100 / value
except ValueError:
    print("Invalid integer")
except ZeroDivisionError:
    print("Cannot divide by zero")
else:
    print("Result:", result)  # only when no exception
finally:
    print("Calculation finished")  # always executes


def set_age(age):
    if age < 0:
        raise ValueError("Age cannot be negative")
    return age
```

## 11. Files
Use `with` to close files automatically. Specify an encoding for text files.

```python
from pathlib import Path
path = Path("notes.txt")
path.write_text("First line\nSecond line\n", encoding="utf-8")
print(path.read_text(encoding="utf-8"))
with path.open("a", encoding="utf-8") as file:
    file.write("Third line\n")
with path.open("r", encoding="utf-8") as file:
    for line in file:
        print(line.rstrip("\n"))
```

File modes: `r` read, `w` write/overwrite, `a` append, `x` create exclusively, `b` binary, `+` read and write. Be careful: `w` replaces existing contents.

## 12. Object-Oriented Programming
Classes define attributes and behavior; objects are instances. `self` refers to the current instance.

```python
class Person:
    species = "Human"  # class attribute

    def __init__(self, name, age):
        self.name = name
        self.age = age

    def introduce(self):
        return f"I'm {self.name}, age {self.age}"

class Student(Person):
    def __init__(self, name, age, course):
        super().__init__(name, age)
        self.course = course

    def introduce(self):  # method override
        return f"{super().introduce()}, studying {self.course}"

student = Student("Ali", 21, "Python")
print(student.introduce())
```

Core concepts:
- **Encapsulation:** bundle state and methods; Python conventions use `_name` for non-public attributes.
- **Inheritance:** derive a class from another class.
- **Polymorphism:** different objects expose compatible methods.
- **Abstraction:** hide implementation details behind clear interfaces.

A `@property` can expose computed attributes, `@classmethod` receives `cls`, and `@staticmethod` receives neither `self` nor `cls` automatically.

```python
from dataclasses import dataclass

@dataclass
class Product:
    name: str
    price: float

item = Product("Milk", 4.50)
print(item)
```

## 13. Modules and Packages
A module is a `.py` file. A package groups modules (commonly in a directory with `__init__.py`).

```python
import math
from datetime import date
from pathlib import Path

print(math.sqrt(25))
print(date.today())
print(Path.cwd())
```

To run code only when a file is executed directly:
```python
def main():
    print("Program started")

if __name__ == "__main__":
    main()
```

## 14. Functional Tools

```python
numbers = [1, 2, 3, 4]
double = lambda x: x * 2
print(list(map(double, numbers)))       # [2, 4, 6, 8]
print(list(filter(lambda x: x % 2 == 0, numbers)))  # [2, 4]
print(list(zip(["a", "b"], [10, 20])))
print(sorted(["pear", "fig", "apple"], key=len))
print(any(x > 3 for x in numbers))
print(all(x > 0 for x in numbers))
```

Often, comprehensions are clearer than `map` or `filter` with a lambda.

## 15. Iterators and Generators
An iterable can provide an iterator; `next()` requests the next item. Generators yield values lazily, avoiding the need to store the full sequence.

```python
values = iter([10, 20])
print(next(values))  # 10
print(next(values))  # 20

def countdown(start):
    while start > 0:
        yield start
        start -= 1

for n in countdown(3):
    print(n)
```

`range()` is an iterable but not itself an iterator.

## 16. Decorators
A decorator wraps or modifies a function. `functools.wraps` preserves function metadata.

```python
from functools import wraps

def log_call(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@log_call
def add(a, b):
    return a + b

print(add(2, 3))
```

## 17. JSON and CSV
JSON is useful for APIs and configuration. CSV is useful for spreadsheet-style records.

```python
import json
import csv
from pathlib import Path

person = {"name": "Ali", "age": 21}
Path("person.json").write_text(json.dumps(person, indent=2), encoding="utf-8")
loaded = json.loads(Path("person.json").read_text(encoding="utf-8"))
print(loaded["name"])

rows = [{"name": "Ali", "score": 90}, {"name": "Sara", "score": 85}]
with open("scores.csv", "w", newline="", encoding="utf-8") as file:
    writer = csv.DictWriter(file, fieldnames=["name", "score"])
    writer.writeheader()
    writer.writerows(rows)
with open("scores.csv", "r", newline="", encoding="utf-8") as file:
    for row in csv.DictReader(file):
        print(row)  # CSV field values are strings
```

## 18. Virtual Environments
A virtual environment isolates dependencies per project.

```bash
python -m venv .venv
# macOS / Linux
source .venv/bin/activate
# Windows PowerShell
.venv\Scripts\Activate.ps1
python -m pip install requests
python -m pip freeze > requirements.txt
python -m pip install -r requirements.txt
deactivate
```

Add `.venv/` to `.gitignore`; do not commit virtual environments or secrets. Use `python -m pip` to target the intended Python interpreter.

## 19. Best Practices
- Use descriptive `snake_case` variable/function names and `PascalCase` class names.
- Follow PEP 8; use four spaces per indentation level.
- Write small functions with one clear responsibility.
- Use type hints for readability; they are not enforced at runtime by default.
- Use `with` for file handling and specific `except` blocks for errors.
- Avoid hardcoding passwords or API keys. Use environment variables or a secrets manager.
- Test behavior with `assert` or a testing framework such as `pytest`.
- Use `.gitignore` for `.venv/`, `__pycache__/`, and local secret files.
- Do not modify a list while iterating over it unless the behavior is carefully controlled.

```python
def calculate_total(prices: list[float]) -> float:
    """Return the sum of item prices."""
    return sum(prices)

assert calculate_total([2.5, 3.0]) == 5.5
```

## 20. Interview Questions

**Q1. `==` vs `is`?** `==` checks equality of values; `is` checks object identity.

**Q2. List vs tuple?** Lists are mutable; tuples are immutable containers.

**Q3. `append()` vs `extend()`?** `append(x)` adds one object; `extend(iterable)` adds its elements.

**Q4. `break` vs `continue`?** `break` exits the loop; `continue` skips to the next iteration.

**Q5. `for` vs `while`?** `for` iterates over an iterable; `while` repeats as long as its condition is true.

**Q6. `return` vs `print`?** `return` provides a function result; `print` writes text to standard output.

**Q7. Shallow vs deep copy?** A shallow copy creates a new outer container but shares nested objects; a deep copy recursively copies nested objects where possible.

```python
import copy
original = [[1], [2]]
shallow = copy.copy(original)
deep = copy.deepcopy(original)
original[0].append(99)
print(shallow)  # [[1, 99], [2]]
print(deep)     # [[1], [2]]
```

**Q8. What is `None`?** A singleton value commonly used to represent the absence of a value; compare with `is None`.

**Q9. What are `*args` and `**kwargs`?** They collect extra positional arguments into a tuple and extra keyword arguments into a dictionary.

**Q10. What is a generator?** An iterator typically created by a function with `yield`, producing values on demand.

**Q11. What is the GIL?** In standard CPython builds, the Global Interpreter Lock generally limits execution of Python bytecode to one thread at a time; free-threaded CPython builds also exist. Threads are still useful for I/O-bound tasks.

**Q12. How do you handle errors?** Use `try`/`except`, catch specific exceptions, and clean up resources with `finally` or context managers.

**Q13. What is a dictionary?** A mutable mapping of hashable keys to values, with efficient average-case lookup.

**Q14. What is slicing?** Extracting a portion of a sequence using `[start:stop:step]`.

**Q15. What does `if __name__ == '__main__'` mean?** Run that block when the module is executed as the main program, not when imported normally.

## 21. Practice Exercises
Try these without looking at solutions first:

1. Print all even numbers from 1 to 50.
2. Count the vowels in a string.
3. Find the largest number in a list without using `max()`.
4. Remove duplicates from a list while keeping the original order.
5. Build a function to calculate a shopping-cart total.
6. Read a CSV file and calculate the average score.
7. Create a `BankAccount` class with deposit and withdrawal methods.
8. Write a function to check whether a word is a palindrome.
9. Catch invalid numeric input without crashing.
10. Save and load a dictionary using JSON.

<details>
<summary>Sample solutions for exercises 1–5</summary>

```python
# 1
print([n for n in range(1, 51) if n % 2 == 0])

# 2
text = "Hello World"
print(sum(char.lower() in "aeiou" for char in text))

# 3
numbers = [5, 12, 3]
largest = numbers[0]
for n in numbers[1:]:
    if n > largest:
        largest = n
print(largest)

# 4
items = [1, 2, 1, 3, 2]
print(list(dict.fromkeys(items)))

# 5
def cart_total(prices):
    return sum(prices)
print(cart_total([10, 15, 5]))
```

</details>

---

**Study tip:** Run each code block, change the inputs, predict the output, and explain *why* it works. Use the [official Python tutorial](https://docs.python.org/3/tutorial/) for deeper reference.
