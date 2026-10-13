Error Handling: Exceptions and Try/Except

**Errors (exceptions)** occur when something goes wrong during program execution (e.g., dividing by zero, file not found). Python lets you **handle** these gracefully using `try`/`except` instead of letting the program crash.

**Syntax:**

```python
try:
    # code that might cause an error
except ExceptionType:
    # code that runs if that error occurs
else:
    # runs if NO error occurred (optional)
finally:
    # always runs, error or not (optional)
```

**a) Basic example:**

```python
try:
    num = int(input("Enter a number: "))
    result = 10 / num
    print("Result:", result)
except ZeroDivisionError:
    print("Error: You cannot divide by zero!")
except ValueError:
    print("Error: Please enter a valid number!")
```

**Sample Run 1 (input = 0):**
```
Enter a number: 0
Error: You cannot divide by zero!
```

**Sample Run 2 (input = "abc"):**
```
Enter a number: abc
Error: Please enter a valid number!
```

**b) Using `else` and `finally`:**

```python
try:
    num = int(input("Enter a number: "))
    result = 10 / num
except ZeroDivisionError:
    print("Cannot divide by zero!")
else:
    print("Success! Result:", result)
finally:
    print("Execution finished (runs no matter what).")
```

**Sample Run (input = 5):**
```
Enter a number: 5
Success! Result: 2.0
Execution finished (runs no matter what).
```

**c) Catching a generic exception (use sparingly):**

```python
try:
    print(10 / 0)
except Exception as e:
    print("Something went wrong:", e)
```

**Output:**
```
Something went wrong: division by zero
```

**d) Common built-in exceptions:**

| Exception            | Occurs when...                            |
|-----------------------|--------------------------------------------|
| `ZeroDivisionError`  | Dividing a number by zero                  |
| `ValueError`         | Invalid value (e.g., `int("abc")`)         |
| `TypeError`          | Wrong data type used in an operation       |
| `FileNotFoundError`  | Trying to open a file that doesn't exist   |
| `IndexError`         | Accessing an invalid list index            |
| `KeyError`           | Accessing a non-existent dictionary key    |

📖 **Official Documentation:** [Errors and Exceptions](https://docs.python.org/3/tutorial/errors.html) | [Built-in Exceptions List](https://docs.python.org/3/library/exceptions.html)

---
