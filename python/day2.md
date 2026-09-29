
## Day 2: First Script, Variables, Data Types & Operators

### 1. Writing Your First Python Script

A **script** is simply a `.py` file containing Python code that you write in an editor (like VS Code, PyCharm, or even Notepad) and run using the Python interpreter — instead of typing line-by-line in the CLI.

**Steps:**
1. Create a new file named `hello.py`
2. Write your code in it
3. Run it from the terminal

**Example — `hello.py`:**

```python
# hello.py
# My first Python script

print("This is my first Python script!")
print("Learning Python step by step.")
```

**Run it:**
```bash
python hello.py
```

**Output:**
```
This is my first Python script!
Learning Python step by step.
```

📖 **Official Documentation:** [Python Tutorial - First Steps](https://docs.python.org/3/tutorial/introduction.html)

---

### 2. Variables

A **variable** is a name that refers to a value stored in memory. In Python, you don't need to declare a type — Python figures it out automatically (called **dynamic typing**).

**Rules for naming variables:**
- Must start with a letter or underscore (`_`), not a number
- Can contain letters, numbers, and underscores
- Case-sensitive (`age` and `Age` are different)
- Cannot use Python reserved keywords (`if`, `for`, `class`, etc.)

**Example:**

```python
name = "Alice"      # string variable
age = 21             # integer variable
height = 5.6         # float variable
is_student = True    # boolean variable

print(name, age, height, is_student)
```

**Output:**
```
Alice 21 5.6 True
```

📖 **Official Documentation:** [Python Tutorial - Variables & Assignment](https://docs.python.org/3/tutorial/introduction.html#using-python-as-a-calculator)

---

### 3. Data Types

Python has several **built-in data types**. The most common basic ones are:

| Data Type | Description                  | Example              |
|-----------|-------------------------------|-----------------------|
| `int`     | Whole numbers                  | `10`, `-5`, `100`    |
| `float`   | Decimal numbers                | `3.14`, `-0.5`       |
| `str`     | Text / string of characters    | `"Hello"`, `'Python'`|
| `bool`    | Boolean (True/False)           | `True`, `False`      |

**Example — checking data types with `type()`:**

```python
a = 10
b = 3.14
c = "Python"
d = True

print(type(a))   # <class 'int'>
print(type(b))   # <class 'float'>
print(type(c))   # <class 'str'>
print(type(d))   # <class 'bool'>
```

**Output:**
```
<class 'int'>
<class 'float'>
<class 'str'>
<class 'bool'>
```

📖 **Official Documentation:** [Python Built-in Types](https://docs.python.org/3/library/stdtypes.html)

---

### 4. Operators

Operators are special symbols used to perform operations on variables and values.

**a) Arithmetic Operators**

| Operator | Meaning         | Example (`a=10, b=3`) | Result |
|----------|-----------------|-------------------------|--------|
| `+`      | Addition        | `a + b`                | `13`   |
| `-`      | Subtraction     | `a - b`                | `7`    |
| `*`      | Multiplication  | `a * b`                | `30`   |
| `/`      | Division        | `a / b`                | `3.33` |
| `//`     | Floor Division  | `a // b`               | `3`    |
| `%`      | Modulus         | `a % b`                | `1`    |
| `**`     | Exponent        | `a ** b`               | `1000` |

**b) Comparison Operators:** `==`, `!=`, `>`, `<`, `>=`, `<=`
**c) Logical Operators:** `and`, `or`, `not`
**d) Assignment Operators:** `=`, `+=`, `-=`, `*=`, `/=`

**Example:**

```python
a = 10
b = 3

print("Sum:", a + b)
print("Difference:", a - b)
print("Product:", a * b)
print("Division:", a / b)
print("Floor Division:", a // b)
print("Modulus:", a % b)
print("Exponent:", a ** b)

# Comparison
print(a > b)   # True

# Logical
print(a > 5 and b < 5)   # True
```

**Output:**
```
Sum: 13
Difference: 7
Product: 30
Division: 3.3333333333333335
Floor Division: 3
Modulus: 1
Exponent: 1000
True
True
```

📖 **Official Documentation:** [Python Operators](https://docs.python.org/3/reference/expressions.html#operator-precedence)

---

### 5. WAC (Write A Code): Simple Mathematical Operations

**Problem:** Write a program to take two numbers as input and perform all basic mathematical operations on them.

```python
# WAC to perform simple mathematical operations

num1 = float(input("Enter first number: "))
num2 = float(input("Enter second number: "))

print("Addition:", num1 + num2)
print("Subtraction:", num1 - num2)
print("Multiplication:", num1 * num2)
print("Division:", num1 / num2)
print("Floor Division:", num1 // num2)
print("Modulus:", num1 % num2)
print("Exponent:", num1 ** num2)
```

**Sample Run:**
```
Enter first number: 10
Enter second number: 4
Addition: 14.0
Subtraction: 6.0
Multiplication: 40.0
Division: 2.5
Floor Division: 2.0
Modulus: 2.0
Exponent: 10000.0
```

> 💡 **Try it yourself:** Modify the program to calculate only the **average** of the two numbers.
