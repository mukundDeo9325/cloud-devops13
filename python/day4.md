## Day 4: Numbers, Strings, Lists, Tuples, Sets & Dictionaries

### 1. Numbers

Python supports several numeric types: `int` (integers), `float` (decimals), and `complex` (complex numbers).

**Example:**

```python
a = 10          # int
b = 3.14        # float
c = 2 + 3j      # complex

print(type(a), type(b), type(c))
print(a + b)          # int + float = float
print(abs(-5))        # absolute value
print(round(3.567, 2))  # rounding
```

**Output:**
```
<class 'int'> <class 'float'> <class 'complex'>
13.14
5
3.57
```

📖 **Official Documentation:** [Python Numeric Types](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex)

---

### 2. Strings

A **string** is a sequence of characters enclosed in single `' '`, double `" "`, or triple `''' '''` quotes. Strings are **immutable** (cannot be changed after creation).

**Example:**

```python
name = "Python"

print(name.upper())        # PYTHON
print(name.lower())        # python
print(len(name))           # 6
print(name[0])             # P (indexing)
print(name[0:3])           # Pyt (slicing)
print(name + " Rocks")     # concatenation
print(name * 2)            # PythonPython (repetition)
```

**Output:**
```
PYTHON
python
6
P
Pyt
Python Rocks
PythonPython
```

📖 **Official Documentation:** [Python Strings](https://docs.python.org/3/library/stdtypes.html#text-sequence-type-str)

---

### 3. Lists

A **list** is an **ordered, mutable (changeable)** collection that can hold items of different data types. Defined using square brackets `[]`.

**Example:**

```python
fruits = ["apple", "banana", "cherry"]

fruits.append("mango")      # add item
fruits[1] = "blueberry"     # modify item
fruits.remove("apple")      # remove item

print(fruits)
print(len(fruits))
```

**Output:**
```
['blueberry', 'cherry', 'mango']
3
```

📖 **Official Documentation:** [Python Lists](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists)

---

### 4. Tuples

A **tuple** is an **ordered, immutable (unchangeable)** collection. Defined using parentheses `()`. Used when data should not change.

**Example:**

```python
coordinates = (10, 20)
print(coordinates[0])   # 10

# coordinates[0] = 15   # ❌ This will raise an error - tuples are immutable
```

**Output:**
```
10
```

📖 **Official Documentation:** [Python Tuples](https://docs.python.org/3/library/stdtypes.html#tuples)

---

### 5. Sets

A **set** is an **unordered collection of unique items** — no duplicates allowed. Defined using curly braces `{}`.

**Example:**

```python
numbers = {1, 2, 3, 3, 2, 1}
print(numbers)          # duplicates removed automatically

numbers.add(4)
numbers.remove(1)
print(numbers)
```

**Output:**
```
{1, 2, 3}
{2, 3, 4}
```

> 💡 Sets are great for removing duplicates and performing operations like union, intersection, and difference.

📖 **Official Documentation:** [Python Sets](https://docs.python.org/3/tutorial/datastructures.html#sets)

---

### 6. Dictionary

A **dictionary** stores data as **key-value pairs**. Defined using curly braces `{}` with `key: value` format. Keys must be unique.

**Example:**

```python
student = {
    "name": "Alice",
    "age": 21,
    "course": "Python"
}

print(student["name"])       # access value by key
student["age"] = 22          # update value
student["grade"] = "A"       # add new key-value pair

print(student)
```

**Output:**
```
Alice
{'name': 'Alice', 'age': 22, 'course': 'Python', 'grade': 'A'}
```

📖 **Official Documentation:** [Python Dictionaries](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)

---

### 7. WAC (Write A Code): Understanding List vs Tuple

**Problem:** Demonstrate the key difference between a List (mutable) and a Tuple (immutable).

```python
# List - mutable
my_list = [1, 2, 3]
print("Original List:", my_list)
my_list[0] = 100          # allowed
print("Modified List:", my_list)

# Tuple - immutable
my_tuple = (1, 2, 3)
print("Original Tuple:", my_tuple)

try:
    my_tuple[0] = 100      # not allowed
except TypeError as e:
    print("Error:", e)
```

**Output:**
```
Original List: [1, 2, 3]
Modified List: [100, 2, 3]
Original Tuple: (1, 2, 3)
Error: 'tuple' object does not support item assignment
```

**Key takeaway:**

| Feature      | List `[]`        | Tuple `()`        |
|--------------|-------------------|---------------------|
| Mutable      | ✅ Yes            | ❌ No               |
| Syntax       | `[1, 2, 3]`       | `(1, 2, 3)`         |
| Use case     | Data that changes | Data that stays fixed (e.g., coordinates) |
| Performance  | Slower            | Slightly faster     |

---

### 8. WAC (Write A Code): Print a Pattern

**Problem:** Print a right-angled triangle star pattern using nested loops.

```python
rows = 5

for i in range(1, rows + 1):
    print("* " * i)
```

**Output:**
```
* 
* * 
* * * 
* * * * 
* * * * * 
```

**Bonus — Number Pattern:**

```python
rows = 5

for i in range(1, rows + 1):
    for j in range(1, i + 1):
        print(j, end=" ")
    print()
```

**Output:**
```
1 
1 2 
1 2 3 
1 2 3 4 
1 2 3 4 5 
```

> 💡 **Try it yourself:** Modify the pattern program to print an **inverted** triangle.
