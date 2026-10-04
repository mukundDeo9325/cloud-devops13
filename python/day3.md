## Day 3: Conditions, Loops, Comments & Loop Control Statements

### 1. Conditional Statements (`if`, `elif`, `else`)

Conditional statements let your program **make decisions** and execute different blocks of code depending on whether a condition is `True` or `False`.

**Syntax:**

```python
if condition1:
    # runs if condition1 is True
elif condition2:
    # runs if condition1 is False and condition2 is True
else:
    # runs if none of the above conditions are True
```

> ⚠️ Python uses **indentation** (spaces) to define code blocks — there are no curly braces `{}` like in C/Java.

**Example:**

```python
age = 20

if age < 13:
    print("You are a child.")
elif age < 20:
    print("You are a teenager.")
else:
    print("You are an adult.")
```

**Output:**
```
You are an adult.
```

**Example — checking even or odd:**

```python
num = 7

if num % 2 == 0:
    print(f"{num} is Even")
else:
    print(f"{num} is Odd")
```

**Output:**
```
7 is Odd
```

📖 **Official Documentation:** [Python `if` Statements](https://docs.python.org/3/tutorial/controlflow.html#if-statements)

---

### 2. Loops

Loops are used to **execute a block of code repeatedly** until a certain condition is met.

#### a) `for` loop

Used when you know the number of iterations in advance (e.g., looping through a sequence).

**Example:**

```python
for i in range(1, 6):
    print(f"Count: {i}")
```

**Output:**
```
Count: 1
Count: 2
Count: 3
Count: 4
Count: 5
```

**Example — looping through a string:**

```python
for letter in "Python":
    print(letter)
```

**Output:**
```
P
y
t
h
o
n
```

📖 **Official Documentation:** [Python `for` Statements](https://docs.python.org/3/tutorial/controlflow.html#for-statements)

#### b) `while` loop

Used when you want to repeat a block of code **as long as a condition is True** (number of iterations may not be known in advance).

**Example:**

```python
count = 1

while count <= 5:
    print(f"Count: {count}")
    count += 1
```

**Output:**
```
Count: 1
Count: 2
Count: 3
Count: 4
Count: 5
```

📖 **Official Documentation:** [Python `while` Statements](https://docs.python.org/3/reference/compound_stmts.html#the-while-statement)

---

### 3. Comments

Comments are used to explain code and are **ignored by the Python interpreter**. They make your code more readable for yourself and others.

**a) Single-line comment** — starts with `#`

```python
# This is a single-line comment
print("Hello")  # This prints Hello
```

**b) Multi-line comment** — Python has no true multi-line comment syntax, but a common convention is to use consecutive `#` lines or a triple-quoted string (technically a string literal, not a comment):

```python
# This is line one of the comment
# This is line two of the comment

"""
This is often used as a
multi-line comment/docstring
"""
print("Comments example")
```

📖 **Official Documentation:** [Python Comments](https://docs.python.org/3/tutorial/introduction.html#using-python-as-a-calculator) *(see also the [Style Guide - PEP 8](https://peps.python.org/pep-0008/#comments))*

---

### 4. `continue`, `break`, and `pass`

These are **loop control statements** used to alter the normal flow of loops.

#### a) `break` — exits the loop immediately

```python
for i in range(1, 10):
    if i == 5:
        break
    print(i)
```

**Output:**
```
1
2
3
4
```

#### b) `continue` — skips the current iteration and moves to the next one

```python
for i in range(1, 6):
    if i == 3:
        continue
    print(i)
```

**Output:**
```
1
2
4
5
```

#### c) `pass` — does nothing; used as a placeholder where code is syntactically required but you don't want to execute anything yet

```python
for i in range(1, 4):
    if i == 2:
        pass   # placeholder, does nothing
    print(i)
```

**Output:**
```
1
2
3
```

> 💡 `pass` is especially useful when writing the structure of a program first (functions, loops, classes) and filling in the logic later.

📖 **Official Documentation:** [Break, Continue, and Pass Statements](https://docs.python.org/3/tutorial/controlflow.html#break-and-continue-statements-and-else-clauses-on-loops)

---

### 5. WAC (Write A Code): Practice Combining Concepts

**Problem:** Print all numbers from 1 to 20, skip multiples of 3, and stop completely if the number exceeds 15.

```python
for num in range(1, 21):
    if num > 15:
        break
    if num % 3 == 0:
        continue
    print(num)
```

**Output:**
```
1
2
4
5
7
8
10
11
13
14
```

> 💡 **Try it yourself:** Use a `while` loop instead of a `for` loop to solve the same problem.
