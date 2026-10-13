## Day 5: Functions, Arguments, Return Values, Built-in Functions & Lambda

### 1. Defining and Calling Functions

A **function** is a reusable block of code that performs a specific task. It helps avoid repetition and makes code organized and modular.

**Syntax:**

```python
def function_name(parameters):
    # code block
    return value   # optional
```

**Example — Defining and calling a simple function:**

```python
def greet():
    print("Hello, welcome to Python!")

# Calling the function
greet()
```

**Output:**
```
Hello, welcome to Python!
```

📖 **Official Documentation:** [Python Functions - Defining Functions](https://docs.python.org/3/tutorial/controlflow.html#defining-functions)

---

### 2. Arguments and Return Values

**Arguments** are values passed into a function so it can use them. **Return values** are the output a function sends back using the `return` keyword.

**a) Function with arguments:**

```python
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")
greet("Bob")
```

**Output:**
```
Hello, Alice!
Hello, Bob!
```

**b) Function with a return value:**

```python
def add(a, b):
    return a + b

result = add(5, 3)
print("Sum:", result)
```

**Output:**
```
Sum: 8
```

**c) Default arguments** — used when no value is passed:

```python
def greet(name="Guest"):
    print(f"Hello, {name}!")

greet()          # uses default value
greet("Alice")   # overrides default
```

**Output:**
```
Hello, Guest!
Hello, Alice!
```

**d) Keyword arguments** — pass values using parameter names, order doesn't matter:

```python
def student_info(name, age):
    print(f"Name: {name}, Age: {age}")

student_info(age=21, name="Alice")
```

**Output:**
```
Name: Alice, Age: 21
```

📖 **Official Documentation:** [Python More on Defining Functions](https://docs.python.org/3/tutorial/controlflow.html#more-on-defining-functions)

---

### 3. Built-in Functions

Python comes with many **built-in functions** that are always available — no need to import anything. Some commonly used ones:

| Function     | Description                          | Example              | Output |
|--------------|---------------------------------------|-----------------------|--------|
| `len()`      | Returns length                        | `len("Python")`      | `6`    |
| `type()`     | Returns data type                     | `type(10)`            | `<class 'int'>` |
| `print()`    | Displays output                       | `print("Hi")`         | `Hi`   |
| `input()`    | Takes user input                      | `input("Enter: ")`    | -      |
| `max()`      | Returns largest value                 | `max(3, 7, 2)`         | `7`    |
| `min()`      | Returns smallest value                | `min(3, 7, 2)`         | `2`    |
| `sum()`      | Returns sum of an iterable            | `sum([1, 2, 3])`      | `6`    |
| `sorted()`   | Returns a sorted list                 | `sorted([3, 1, 2])`   | `[1, 2, 3]` |
| `range()`    | Generates a sequence of numbers       | `list(range(3))`      | `[0, 1, 2]` |

**Example:**

```python
numbers = [5, 3, 9, 1, 7]

print("Length:", len(numbers))
print("Max:", max(numbers))
print("Min:", min(numbers))
print("Sum:", sum(numbers))
print("Sorted:", sorted(numbers))
```

**Output:**
```
Length: 5
Max: 9
Min: 1
Sum: 25
Sorted: [1, 3, 5, 7, 9]
```

📖 **Official Documentation:** [Python Built-in Functions](https://docs.python.org/3/library/functions.html)

---

### 4. Lambda Function

A **lambda function** is a small, anonymous (unnamed) function defined using the `lambda` keyword. It can take any number of arguments but has only **one expression**. Useful for short, throwaway functions.

**Syntax:**

```python
lambda arguments: expression
```

**Example — Regular function vs Lambda function:**

```python
# Regular function
def square(x):
    return x * x

# Equivalent lambda function
square_lambda = lambda x: x * x

print(square(5))          # 25
print(square_lambda(5))   # 25
```

**Output:**
```
25
25
```

**Example — Lambda with multiple arguments:**

```python
add = lambda a, b: a + b
print(add(4, 6))
```

**Output:**
```
10
```

**Example — Lambda used with `map()` and `filter()` (common real-world use case):**

```python
numbers = [1, 2, 3, 4, 5, 6]

# Square every number using map()
squares = list(map(lambda x: x * x, numbers))
print("Squares:", squares)

# Get only even numbers using filter()
evens = list(filter(lambda x: x % 2 == 0, numbers))
print("Evens:", evens)
```

**Output:**
```
Squares: [1, 4, 9, 16, 25, 36]
Evens: [2, 4, 6]
```

> 💡 **When to use lambda:** For short, simple functions used once (often inside `map()`, `filter()`, or `sorted()`). For anything more complex, use a regular `def` function for readability.

📖 **Official Documentation:** [Python Lambda Expressions](https://docs.python.org/3/reference/expressions.html#lambda)

---

### 5. WAC (Write A Code): Practice with Functions

**Problem:** Write a function that takes a list of numbers and returns the even numbers using a regular function, then solve the same problem using a lambda with `filter()`.

```python
# Using a regular function
def get_evens(nums):
    result = []
    for n in nums:
        if n % 2 == 0:
            result.append(n)
    return result

numbers = [10, 15, 22, 33, 40, 51]
print("Using function:", get_evens(numbers))

# Using lambda + filter
print("Using lambda:", list(filter(lambda x: x % 2 == 0, numbers)))
```

**Output:**
```
Using function: [10, 22, 40]
Using lambda: [10, 22, 40]
```

> 💡 **Try it yourself:** Write a lambda function that returns `True` if a number is a palindrome-like check on strings (e.g., checks if a string reads the same backward).
> 
