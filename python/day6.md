## Day 6: Modules, Standard Libraries & Arrays with NumPy

### 1. Importing Modules

A **module** is simply a `.py` file containing Python code (functions, classes, variables) that you can reuse in other programs. Python allows you to **import** modules to use their functionality without rewriting code.

**Ways to import a module:**

```python
# 1. Import the whole module
import math
print(math.sqrt(16))

# 2. Import a specific function/attribute
from math import sqrt
print(sqrt(25))

# 3. Import with an alias
import math as m
print(m.pi)

# 4. Import everything (not recommended - can cause naming conflicts)
from math import *
print(pow(2, 3))
```

**Output:**
```
4.0
5.0
3.141592653589793
8.0
```

> 💡 You can also create your **own module** — just save a `.py` file (e.g., `mymodule.py`) and `import mymodule` in another script in the same folder.

📖 **Official Documentation:** [Python Modules](https://docs.python.org/3/tutorial/modules.html)

---

### 2. Standard Libraries

Python comes with a huge collection of built-in modules called the **Standard Library** — no installation needed, just `import` and use.

#### a) `os` module — interact with the operating system

```python
import os

print(os.getcwd())          # get current working directory
print(os.listdir("."))      # list files/folders in current directory
# os.mkdir("new_folder")    # create a new folder
```

**Sample Output:**
```
/home/user/projects
['day1.md', 'day2.md', 'script.py']
```

📖 **Official Documentation:** [`os` — Miscellaneous OS interfaces](https://docs.python.org/3/library/os.html)

#### b) `sys` module — interact with the Python interpreter/system

```python
import sys

print(sys.version)          # Python version being used
print(sys.platform)         # OS platform (e.g., 'linux', 'win32')
# sys.exit()                 # exits the program
```

**Sample Output:**
```
3.12.4 (main, Jun  1 2024, 10:00:00)
linux
```

📖 **Official Documentation:** [`sys` — System-specific parameters and functions](https://docs.python.org/3/library/sys.html)

#### c) `time` module — work with time-related tasks

```python
import time

print("Start")
time.sleep(2)     # pauses execution for 2 seconds
print("End (after 2 seconds)")

print("Current time (epoch):", time.time())
```

**Sample Output:**
```
Start
End (after 2 seconds)
Current time (epoch): 1732458213.123456
```

📖 **Official Documentation:** [`time` — Time access and conversions](https://docs.python.org/3/library/time.html)

#### d) `random` module (bonus — very commonly used)

```python
import random

print(random.randint(1, 10))      # random integer between 1-10
print(random.choice(["a", "b", "c"]))  # random item from a list
```

**Sample Output (varies each run):**
```
7
b
```

📖 **Official Documentation:** [`random` — Generate pseudo-random numbers](https://docs.python.org/3/library/random.html)

📖 **Full list of Standard Library modules:** [Python Standard Library Index](https://docs.python.org/3/library/index.html)

---
