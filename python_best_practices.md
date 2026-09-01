<!-- omit in toc -->
# Lecture 1b - Python Best Practices

<!-- omit in toc -->
## Lecture Information

| **Master's Degree** | Intelligent Internet of Things (D.M.270/04)                                      |
|---------------------|----------------------------------------------------------------------------------|
| **Course**          | Intelligent Internet of Things                                                   |
| **Lecture Title**   | Python Best Practices                                                            |
| **Author**          | Prof. Marco Picone (marco.picone@unimore.it)                                     |
| **License**         | [Creative Commons Attribution 4.0](https://creativecommons.org/licenses/by/4.0/) | 


<!-- omit in toc -->
# Table of Contents

- [1b.1 Introduction](#1b1-introduction)
- [1b.2 Understanding `if __name__ == "__main__":`](#1b2-understanding-if-__name__--__main__)
  - [1b.2.1 What `__name__` Is, and What the Guard Does](#1b21-what-__name__-is-and-what-the-guard-does)
  - [1b.2.2 Why the Guard Matters: the Problem It Prevents](#1b22-why-the-guard-matters-the-problem-it-prevents)
  - [1b.2.3 Best Practices](#1b23-best-practices)
- [1b.3 Exception Handling in Python](#1b3-exception-handling-in-python)
  - [1b.3.1 try / except](#1b31-try--except)
  - [1b.3.2 else & finally](#1b32-else--finally)
  - [1b.3.3 Custom Exceptions](#1b33-custom-exceptions)
- [1b.4 Python Data Types & Type Hints](#1b4-python-data-types--type-hints)
  - [1b.4.1 The Cost of Dynamic Typing: Errors a Compiler Would Catch](#1b41-the-cost-of-dynamic-typing-errors-a-compiler-would-catch)
  - [1b.4.2 Writing Robust Code Without Type Hints](#1b42-writing-robust-code-without-type-hints)
  - [1b.4.3 Checking Types at Runtime with `isinstance()`](#1b43-checking-types-at-runtime-with-isinstance)
  - [1b.4.4 A Quick Tour of Python's Built-in Data Types](#1b44-a-quick-tour-of-pythons-built-in-data-types)
  - [1b.4.5 Type Hints: Syntax and Purpose](#1b45-type-hints-syntax-and-purpose)
  - [1b.4.6 Let's See How It Works](#1b46-lets-see-how-it-works)
  - [1b.4.7 A Complete Example: Type Hints and `isinstance()` Together](#1b47-a-complete-example-type-hints-and-isinstance-together)
- [1b.5 Stop Hardcoding Things: Managing Parameters and Configurations](#1b5-stop-hardcoding-things-managing-parameters-and-configurations)
  - [1b.5.1 The Problem with Hardcoded Values](#1b51-the-problem-with-hardcoded-values)
  - [1b.5.2 Command-Line Arguments](#1b52-command-line-arguments)
  - [1b.5.3 Environment Variables](#1b53-environment-variables)
  - [1b.5.4 Configuration Files: YAML and JSON](#1b54-configuration-files-yaml-and-json)
- [1b.6 Logging](#1b6-logging)
  - [1b.6.1 Why Not Just `print()`?](#1b61-why-not-just-print)
  - [1b.6.2 The `logging` Module Basics](#1b62-the-logging-module-basics)
  - [1b.6.3 Logging to a File](#1b63-logging-to-a-file)
- [1b.7 Managing Dependencies](#1b7-managing-dependencies)
  - [1b.7.1 `requirements.txt`](#1b71-requirementstxt)
  - [1b.7.2 `pyproject.toml`](#1b72-pyprojecttoml)
- [1b.8 Virtual Environments](#1b8-virtual-environments)
  - [1b.8.1 What Is a Virtual Environment, and Why It Matters](#1b81-what-is-a-virtual-environment-and-why-it-matters)
  - [1b.8.2 Creating and Using a Virtual Environment with `venv`](#1b82-creating-and-using-a-virtual-environment-with-venv)
  - [1b.8.3 Virtual Environments and Dependency Management, Together](#1b83-virtual-environments-and-dependency-management-together)
- [1b.9 Python Project Layout](#1b9-python-project-layout)
- [1b.10 Python Modules & Packages](#1b10-python-modules--packages)
  - [1b.10.1 Modules and `import`](#1b101-modules-and-import)
  - [1b.10.2 Packages and `__init__.py`](#1b102-packages-and-__init__py)
  - [1b.10.3 Absolute vs. Relative Imports](#1b103-absolute-vs-relative-imports)
- [1b.11 Faster Project & Dependency Management with `uv`](#1b11-faster-project--dependency-management-with-uv)
  - [1b.11.1 What Is `uv`?](#1b111-what-is-uv)
  - [1b.11.2 Initializing a Project with the `src/` Layout Already Covered](#1b112-initializing-a-project-with-the-src-layout-already-covered)
  - [1b.11.3 Dependencies and the Virtual Environment, Handled Automatically](#1b113-dependencies-and-the-virtual-environment-handled-automatically)
  - [1b.11.4 Running One or More `main`s from the Command Line](#1b114-running-one-or-more-mains-from-the-command-line)

# 1b.1 Introduction

This lecture sits between **Lecture 1** (Cyber-Physical Systems & IoT introduction) and **Lecture 2** (Python Object-Oriented Programming). Before diving into classes and objects, it is worth pausing on a set of practices that make *any* Python code — object-oriented or not — more robust, readable, and maintainable in a real project.

None of the topics below require OOP to be useful: they apply just as much to a short script as to a large, class-based codebase. But they *do* make the transition into OOP (Lecture 2) smoother, since good habits around typing, configuration, error handling, and project structure are exactly what turns a collection of classes into a maintainable piece of software.

**What this lecture covers:**
- What `if __name__ == "__main__":` actually does, and why so much example code in this lecture (and beyond) uses it.
- Exception handling in Python.
- Python data types and type hints — and, just as importantly, what goes wrong without them.
- Getting rid of hardcoded values: command-line arguments, environment variables, configuration files.
- Logging, as a replacement for scattering `print()` statements everywhere.
- Managing dependencies with `requirements.txt` and `pyproject.toml`.
- Virtual environments, and how they work together with dependency management.
- How to lay out a Python project.
- Modules, packages, and the difference between absolute and relative imports.
- `uv`, a modern tool that combines dependency management, virtual environments, and project scaffolding into one faster workflow.

---

# 1b.2 Understanding `if __name__ == "__main__":`

## 1b.2.1 What `__name__` Is, and What the Guard Does

Every Python file, the moment it runs, has access to a built-in variable called `__name__`, set automatically by the interpreter — nothing has to define it. Its value depends entirely on **how the file started running**:

- If the file is the one Python was told to run directly (`python3 sensor_utils.py`), `__name__` is set to the string `"__main__"`.
- If the same file is instead **imported** from somewhere else (`import sensor_utils`), `__name__` is set to the module's own name (`"sensor_utils"`) instead.

`if __name__ == "__main__":` is simply a check against this value. Code placed inside that `if` block only runs when the file is executed directly — never when it is imported.

```python
# sensor_utils.py
def read_temperature():
    return 21.5

print(f"sensor_utils loaded, __name__ is {__name__!r}")

if __name__ == "__main__":
    print("Running sensor_utils.py directly - starting the sensor loop...")
    print(read_temperature())
```

Running this file directly:

```
$ python3 sensor_utils.py
sensor_utils loaded, __name__ is '__main__'
Running sensor_utils.py directly - starting the sensor loop...
21.5
```

Importing the exact same file from another script instead:

```python
# main_app.py
import sensor_utils

print("main_app.py just needed the function, nothing else:")
print(sensor_utils.read_temperature())
```

```
$ python3 main_app.py
sensor_utils loaded, __name__ is 'sensor_utils'
main_app.py just needed the function, nothing else:
21.5
```

Same file, same `def read_temperature():` — but the guarded block only fires when `sensor_utils.py` is the file being run directly. The unguarded `print(...)` right above it, by contrast, runs **every single time** the file is loaded at all, whether run directly or imported — which leads directly to the next section.

## 1b.2.2 Why the Guard Matters: the Problem It Prevents

The first time Python imports a file, it does not just register the functions and classes defined in it — it runs the **entire file, top to bottom**, exactly once. Any code sitting at the top level, outside of a function and outside an `if __name__ == "__main__":` guard, runs as a side effect of that import, whether or not anyone wanted it to.

```python
# sensor_utils_bad.py
def read_temperature():
    return 21.5

# BUG: no guard - this runs immediately, whether the file is executed or imported
print("Starting the sensor loop...")
print(read_temperature())
```

```python
# main_app_bad.py
import sensor_utils_bad   # just wanted the function - but the "sensor loop" starts anyway

print("main_app_bad.py just needed the function, nothing else:")
print(sensor_utils_bad.read_temperature())
```

```
$ python3 main_app_bad.py
Starting the sensor loop...
21.5
main_app_bad.py just needed the function, nothing else:
21.5
```

`main_app_bad.py` only wanted `read_temperature()` — but merely *importing* `sensor_utils_bad` was enough to trigger "Starting the sensor loop..." and a reading, before `main_app_bad.py`'s own code even got a chance to run. In a larger project this is exactly the kind of surprising, hard-to-trace behavior this lecture has warned about since Section 1b.4.1: unrelated code executing as a side effect of something that looked like a harmless import.

## 1b.2.3 Best Practices

- **Guard any top-level code that has a real effect** — starting a loop, printing, reading a sensor, opening a connection — behind `if __name__ == "__main__":`, even in a small, throwaway script. It costs nothing, and it guarantees the file can always be imported safely later, even if that was not the original plan.
- **Keep the guarded block minimal.** Define a `main()` function with the actual logic, and let the guard do nothing but call it:
  ```python
  def main() -> None:
      print("Running sensor_utils.py directly - starting the sensor loop...")
      print(read_temperature())


  if __name__ == "__main__":
      main()
  ```
  This keeps the "am I being run directly?" check separate from the logic itself, which is easier to read, and easier to reuse or test later (Lecture 2 and beyond) without needing to fight the module-execution machinery to do it.
- **A pure "library" file that is only ever meant to be imported does not need the guard at all** — it is only necessary for files that are *also* meant to be run directly.
- This is precisely the pattern already relied on throughout the rest of this lecture — the `utils.py` relative-imports example (Section 1b.10.3) and the `interface/cli.py` example in the `uv` section (Section 1b.11.4) both use exactly this guard — and it is also *why* `python -m package.module` and `uv run` are able to "run" a module in the first place: executing a module that way sets that module's own `__name__` to `"__main__"`, the exact same mechanism demonstrated above.

---

# 1b.3 Exception Handling in Python

Exception management lets a program **handle errors gracefully** instead of crashing, using `try`-`except` blocks to catch and respond to problems that occur while it runs.

## 1b.3.1 try / except

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Error: Division by zero is not allowed.")
```

**Output:**
```
Error: Division by zero is not allowed.
```

Multiple `except` clauses can handle different error types separately, and `except Exception as e` captures the error message itself:

```python
try:
    value = int("abc")
    print(10 / value)
except ValueError:
    print("Error: Could not convert string to integer.")
except ZeroDivisionError:
    print("Error: Division by zero.")
except Exception as e:
    print(f"Other error: {e}")
```

**Output:**
```
Error: Could not convert string to integer.
```

## 1b.3.2 else & finally

- **`else`** runs only if the `try` block raised **no** exception.
- **`finally`** always runs, whether an exception occurred or not — the natural place for cleanup (closing files, releasing resources).

```python
try:
    value = int("42")
    print("Conversion successful.")
except ValueError:
    print("Conversion failed.")
else:
    print("No errors occurred.")
finally:
    print("This always executes.")
```

**Output:**
```
Conversion successful.
No errors occurred.
This always executes.
```

## 1b.3.3 Custom Exceptions

Python lets you define your own exception types by inheriting from the built-in `Exception` class, to signal specific error conditions that the standard exceptions do not capture well.

> This uses the `class` syntax and inheritance, which Lecture 2 covers in full detail. For now, it is enough to know the pattern below: `class SomeError(Exception): ...` defines a new, custom kind of error that can be raised and caught just like any built-in one.

```python
class ConfigError(Exception):
    """Raised when a required configuration value is missing."""
    def __init__(self, key, message="Missing required configuration value"):
        self.key = key
        self.message = message
        super().__init__(f"{message}: '{key}'")


def load_setting(config, key):
    if key not in config:
        raise ConfigError(key)
    return config[key]


try:
    load_setting({"device_id": "sensor_1"}, "sample_rate_seconds")
except ConfigError as e:
    print(f"Custom Exception Caught: {e}")
```

**Output:**
```
Custom Exception Caught: Missing required configuration value: 'sample_rate_seconds'
```

**Key Points:**
- `try`/`except` prevents an unhandled error from crashing the whole program.
- `else` runs only without errors; `finally` always runs — the place for cleanup code.
- Custom exceptions (inheriting from `Exception`) make error handling more specific and descriptive than relying only on generic built-in exceptions.

---

# 1b.4 Python Data Types & Type Hints

## 1b.4.1 The Cost of Dynamic Typing: Errors a Compiler Would Catch

Python is a **dynamically typed** language: a variable's type is checked only *while the program is running*, not before. There is no compilation step that inspects the whole program up front and rejects it if a function is called with the wrong type of argument — that step simply does not exist in Python. This is convenient (less ceremony, faster to write), but it has a real cost: **type-related bugs only surface when the exact line of code that misuses a type actually executes**, which can be much later, much further away from the mistake, than the moment the bug was introduced.

The most basic illustration of this is a single variable simply changing type over its own lifetime:

```python
a = 10
print(type(a), a)   # <class 'int'> 10

a = "ten"
print(type(a), a)   # <class 'str'> ten
```

**Output:**
```
<class 'int'> 10
<class 'str'> ten
```

Nothing in Python prevents this: `a` starts out holding an `int`, and is later reassigned to hold a completely unrelated type, a `str` — same variable, same name, two different kinds of value at two different points in the program. In a statically typed language (e.g. Java, C#), this would not even compile: a variable's type is fixed once declared, and assigning a string to an `int` variable is rejected before the program ever runs, not something that could happen while it is running.

On its own, this is not yet a bug — but it already shows why a vague, reused name like `a` is risky: nothing about the name signals what `a` is supposed to hold, and nothing in the language stops it from silently becoming something else halfway through a function, especially in a longer piece of code where the reassignment and the eventual misuse are far apart. The two examples below show how that same flexibility leads to real crashes.

**Example 1 — a function that "forgets" to return something on one branch**

```python
def get_discount(code):
    if code == "SUMMER":
        return 0.20
    elif code == "WINTER":
        return 0.10
    # BUG: no branch for any other code -> the function implicitly returns None


price = 100
discount = get_discount("SPRING")          # SPRING is not handled -> discount is None
final_price = price - (price * discount)   # blows up here, not where the bug actually is
```

**Output:**
```
Traceback (most recent call last):
  ...
TypeError: unsupported operand type(s) for *: 'int' and 'NoneType'
```

The bug is the missing branch inside `get_discount()`. But the program does not fail there — it fails several lines later, at `price * discount`, and only for discount codes nobody thought to test. In a larger codebase, `get_discount()` and `price * discount` could easily live in different files, written by different people, months apart.

**Example 2 — passing an object of the wrong type**

```python
def get_user_email(user):
    return user["email"]


class User:
    def __init__(self, email):
        self.email = email


u = User("alice@example.com")
print(get_user_email(u))
```

**Output:**
```
Traceback (most recent call last):
  ...
TypeError: 'User' object is not subscriptable
```

`get_user_email()` was written assuming `user` is a dictionary (`user["email"]`), but nothing in the language stopped someone from calling it with a `User` object instead — the mismatch is only caught the moment `user["email"]` actually runs.

**Example 3 — iterating over a list of mixed types**

Python lists can freely mix types — nothing stops a list from holding an `int`, a `str`, and a `bool` side by side. A common, realistic bug: sensor readings coming from an external source (a file, an API) where one value slipped through as a string instead of a number, or a boolean flag accidentally ended up mixed in with numeric data:

```python
sensor_values = [21, 19, True, "22.5", 20]

total = 0
for value in sensor_values:
    total += value

print(total / len(sensor_values))
```

**Output:**
```
Traceback (most recent call last):
  ...
TypeError: unsupported operand type(s) for +=: 'int' and 'str'
```

Walking through the loop shows two very different failure modes:

- `21`, `19`: totally fine, `total` becomes `40`.
- `True`: **no crash at all** — in Python, `bool` is a subclass of `int`, so `True` is silently treated as `1` and `total` becomes `41`. Nothing signals that a boolean flag has no business being averaged with sensor readings; the result is simply, quietly, wrong.
- `"22.5"`: **only here** does the loop actually crash, with a `TypeError` — even though `"22.5"` "looks like" a number, Python does not treat a string as one just because it reads like one.

This is the same lesson as Examples 1 and 2, taken further: mixing types in a single list is completely legal in Python, the loop that processes it looks perfectly ordinary, and the two kinds of mismatch it contains behave in *opposite* ways — one crashes loudly, the other corrupts the result silently and without any error at all.

**Key Points:**
- Python performs **no static type checking** before running a program — there is no compilation step to reject an incompatible call in advance.
- A type-related bug can be introduced in one place and only manifest as a crash somewhere completely different, often far later in the program's execution.
- This is the same underlying issue already discussed in Lecture 2 regarding Python and interfaces: Python has no compiler to catch a missing method or a wrong type ahead of time — everything is discovered at runtime, sometimes only when a specific, rarely-exercised code path runs.

---

## 1b.4.2 Writing Robust Code Without Type Hints

Type hints (Section 1b.4.5) are one answer to the problem above, but they are optional, and plenty of professional Python code does not use them at all. Regardless of whether hints are used, a few habits go a long way toward avoiding the exact class of bugs shown above.

**1. Use descriptive, specific variable names.** A name should make it obvious what kind of value a variable holds, even without looking at where it was created.

```python
# Poor: types have to be guessed from context, or from reading the whole function
def process(d):
    result = []
    for x in d:
        result.append(x * 2)
    return result


# Better: names alone tell you these are prices, and that the result is a list of prices
def double_all_prices(prices):
    doubled_prices = []
    for price in prices:
        doubled_prices.append(price * 2)
    return doubled_prices
```

Both functions do exactly the same thing — but only the second one lets a reader (or the same author, six months later) tell what `prices` is supposed to be without reading anything else.

**2. Make sure a function always returns the same *kind* of value.** The bug in Example 1 above happened because `get_discount()` returned a `float` on two branches and `None` (implicitly) on every other input. Adding an explicit `else` branch fixes it completely:

```python
def get_discount(code):
    if code == "SUMMER":
        return 0.20
    elif code == "WINTER":
        return 0.10
    else:
        return 0.0   # always a float, never None


price = 100
discount = get_discount("SPRING")
final_price = price - (price * discount)
print(final_price)
```

**Output:**
```
100.0
```

**3. Use docstrings to document the expected types**, especially for functions with several parameters, as a lightweight, always-available fallback when hints are not used:

```python
def double_all_prices(prices):
    """
    Double every price in a list.

    Parameters:
        prices (list[float]): the prices to double.

    Returns:
        list[float]: the doubled prices, in the same order.
    """
    return [price * 2 for price in prices]
```

**Key Points:**
- Descriptive naming and a docstring cost nothing at runtime, yet they communicate exactly the information a type hint would — just informally, and unchecked by any tool.
- Guaranteeing a function always returns the same kind of value on every branch removes an entire category of bugs like the one in Section 1b.4.1.
- These habits are good practice **regardless of whether type hints are used** — and, as the next sections show, they are exactly what type hints go on to formalize.

---

## 1b.4.3 Checking Types at Runtime with `isinstance()`

The three naming/return/docstring habits above are about *preventing* type confusion. `isinstance()` is a complementary tool for *defending* against it: it lets code check, while running, whether a value actually is the type it is supposed to be, before doing anything with it.

```python
value = 42
print(isinstance(value, int))            # True
print(isinstance(value, str))            # False
print(isinstance(value, (int, float)))   # True - a tuple of types means "any of these"
```

**Output:**
```
True
False
True
```

Applied to the mixed `sensor_values` list from Example 3 (Section 1b.4.1), `isinstance()` turns a crash into a controlled, predictable skip:

```python
sensor_values = [21, 19, True, "22.5", 20]

total = 0
valid_count = 0
for value in sensor_values:
    if isinstance(value, bool):
        print(f"Skipping {value!r}: a boolean, not a real measurement")
        continue
    if isinstance(value, (int, float)):
        total += value
        valid_count += 1
    else:
        print(f"Skipping {value!r}: not a number")

print(total / valid_count)
```

**Output:**
```
Skipping True: a boolean, not a real measurement
Skipping '22.5': not a number
20.0
```

Notice the extra `isinstance(value, bool)` check *before* the `isinstance(value, (int, float))` one. This is not redundant: in Python, `bool` is a **subclass** of `int`, so `isinstance(True, int)` is itself `True` —

```python
print(isinstance(True, int))    # True  -> bool is a subclass of int
print(isinstance(True, bool))   # True
```

— meaning a plain `isinstance(value, (int, float))` check would happily let `True` slip through as if it were a legitimate number, silently reproducing the exact bug from Example 3. Checking for `bool` first, and handling it separately, is the standard way around this specific gotcha.

**Key Points:**
- `isinstance(value, SomeType)` returns `True`/`False` at runtime — unlike a type hint, this check actually executes and can change what the program does.
- Passing a tuple of types, `isinstance(value, (int, float))`, checks "is it any of these."
- `bool` is a subclass of `int` in Python: `isinstance(True, int)` is `True`. Any type check meant to accept numbers but reject booleans must test for `bool` explicitly, and before the general numeric check.

---

## 1b.4.4 A Quick Tour of Python's Built-in Data Types

Before formalizing anything with hints, it helps to have the core built-in types in view:

| Type | Example | Mutable? |
|---|---|---|
| `int` | `42` | No |
| `float` | `3.14` | No |
| `str` | `"hello"` | No |
| `bool` | `True` | No |
| `list` | `[1, 2, 3]` | Yes |
| `tuple` | `(1, 2, 3)` | No |
| `dict` | `{"a": 1}` | Yes |
| `set` | `{1, 2, 3}` | Yes |
| `NoneType` | `None` | No |

```python
print(type(42))          # <class 'int'>
print(type(3.14))        # <class 'float'>
print(type("hello"))     # <class 'str'>
print(type(True))        # <class 'bool'>
print(type([1, 2, 3]))   # <class 'list'>
print(type((1, 2, 3)))   # <class 'tuple'>
print(type({"a": 1}))    # <class 'dict'>
print(type({1, 2, 3}))   # <class 'set'>
print(type(None))        # <class 'NoneType'>
```

**Key Points:**
- Every value in Python has a type, checked with the built-in `type()` function — even though nothing forces a variable to keep holding the same type over its lifetime.
- **Mutable** types (`list`, `dict`, `set`) can be changed in place after creation; **immutable** types (`int`, `float`, `str`, `tuple`, `bool`) cannot — any "change" actually creates a new value.

---

## 1b.4.5 Type Hints: Syntax and Purpose

**Type hints** let you annotate variables, function parameters, and return values with the type they are expected to hold. They **formalize**, in a way tools can check, exactly what Section 1b.4.2 was doing informally through naming and docstrings.

```python
name: str = "Alice"
age: int = 30
is_admin: bool = False

def double_all_prices(prices: list[float]) -> list[float]:
    doubled_prices: list[float] = []
    for price in prices:
        doubled_prices.append(price * 2)
    return doubled_prices


print(double_all_prices([10.0, 20.0, 30.0]))
```

**Output:**
```
[20.0, 40.0, 60.0]
```

- `name: str` annotates a variable.
- `prices: list[float]` annotates a parameter — a list of floats.
- `-> list[float]` annotates the **return type** of the function.

A value can also be optional — either a real value, or `None`:

```python
def find_user(user_id: int) -> str | None:
    users = {1: "Alice", 2: "Bob"}
    return users.get(user_id)


print(find_user(1))    # Alice
print(find_user(99))   # None
```

> **Note:** the `list[float]` and `str | None` syntax shown here is the modern form, available from Python 3.9/3.10 onward. On older versions of Python, the same ideas are expressed through the `typing` module instead: `List[float]` and `Optional[str]` from `from typing import List, Optional`. Both forms mean exactly the same thing — prefer the modern syntax unless you specifically need to support an older Python version.

**Key Points:**
- Type hints are written as `name: type` for variables/parameters, and `-> type` for return values.
- They can express "either this type or `None`" with `X | None`.
- They are the same information a good docstring or naming convention already communicates (Section 1b.4.2) — just in a form tools understand.

---

## 1b.4.6 Let's See How It Works

Type hints are **not enforced by Python itself** — they are purely documentation, unless a separate tool checks them. Rewriting the discount function with hints does not change its runtime behavior at all:

```python
def get_discount(code: str) -> float:
    if code == "SUMMER":
        return 0.20
    elif code == "WINTER":
        return 0.10
    else:
        return 0.0


print(get_discount(123))   # 123 is an int, not a str - the hint says this shouldn't happen
```

**Output:**
```
0.0
```

Python runs this without complaint: `code: str` is a hint, not a rule, so passing `123` — an `int` — is not blocked in any way, and the code happens to still "work" because `123 == "SUMMER"` and `123 == "WINTER"` are simply `False`. This is exactly where a **static type checker** such as [`mypy`](https://mypy-lang.org/) comes in: run as a separate command (`mypy your_script.py`), it reads the hints and reports mismatches *before* the program ever runs — for the call above, it would report something like:

```
error: Argument 1 to "get_discount" has incompatible type "int"; expected "str"
```

Most IDEs (VS Code, PyCharm, ...) run a type checker like this continuously in the background, underlining the mismatch as you type — turning what would otherwise be a runtime surprise (Section 1b.4.1) into an error you see immediately, while writing the code.

---

## 1b.4.7 A Complete Example: Type Hints and `isinstance()` Together

This example puts everything from this section (Section 1b.4) into a single, small IoT-flavored piece of code: type hints on variables and functions (Section 1b.4.5), `isinstance()` checks where a value actually needs to be protected (Section 1b.4.3), and comments spelling out exactly which parts are only documentation and which parts are doing real work. It is deliberately written with plain functions, not classes — classes are the subject of Lecture 2.

```python
def add_reading(readings: list[float], value: float) -> None:
    # `readings: list[float]` and `value: float` are documentation - by themselves
    # they would NOT stop a string from being passed (Section 1b.4.6). The
    # isinstance() check below is what ACTUALLY enforces it, and it deliberately
    # excludes bool too (Section 1b.4.3): bool is a subclass of int, so a plain
    # `isinstance(value, (int, float))` would wrongly accept True/False as if
    # they were real readings.
    if not isinstance(value, (int, float)) or isinstance(value, bool):
        raise TypeError(f"reading must be a number, got {type(value).__name__}")
    readings.append(float(value))


def average_reading(readings: list[float]) -> float | None:
    # `-> float | None` is documentation: it tells a reader (and a type checker)
    # that this function can return "no value yet". The `if not readings` check
    # below is what actually MAKES that true - remove it, and this function
    # would raise ZeroDivisionError when called with no readings at all.
    if not readings:
        return None
    return sum(readings) / len(readings)
```

Using it shows both halves in action — the check that works, and the hint that does not:

```python
sensor_1_readings: list[float] = []   # documentation only: "this will hold floats"
add_reading(sensor_1_readings, 21.5)
add_reading(sensor_1_readings, 22.0)
print(average_reading(sensor_1_readings))

try:
    add_reading(sensor_1_readings, "not-a-number")   # blocked by isinstance(), not by the hint
except TypeError as e:
    print(f"Rejected: {e}")

sensor_2_readings: list[float] = []
print(average_reading(sensor_2_readings))   # no readings yet -> None, not a crash

# The hint below (`str`) is never checked anywhere - so this "works" fine,
# even though 12345 is an int, not a str:
device_id: str = 12345
print(device_id, type(device_id).__name__)
```

**Output:**
```
21.75
Rejected: reading must be a number, got str
None
12345 int
```

**Key Points:**
- Every type hint in this example (`readings: list[float]`, `value: float`, `-> float | None`, `device_id: str`) is documentation — none of them, by themselves, stop the wrong kind of value from being used.
- `add_reading()`'s `isinstance()` check is what actually protects `readings` from bad data; `average_reading()`'s `if not readings` check is what actually makes the `float | None` return type true, rather than just claimed.
- `device_id: str = 12345` shows this gap directly: the `str` hint is silently violated, and nothing notices — a reminder that a hint left unchecked is exactly as enforceable as a comment.

---

# 1b.5 Stop Hardcoding Things: Managing Parameters and Configurations

## 1b.5.1 The Problem with Hardcoded Values

A **hardcoded value** is a value written directly into the source code — a file path, a server address, a threshold, a secret key — instead of being supplied from the outside.

```python
# Hardcoded: works on the author's machine, on that exact day, for that exact device
def read_temperature():
    device_path = "/dev/ttyUSB0"
    threshold = 25.0
    ...
```

Hardcoding causes real problems as soon as code has to run somewhere other than the author's own machine:

- **Inflexibility**: changing the device path, a threshold, or a server address requires **editing and redeploying the code**, instead of just changing a value.
- **Security risk**: hardcoded secrets (API keys, passwords, tokens) end up committed to version control, visible to anyone with access to the repository.
- **No environment separation**: the same code cannot easily be pointed at a "development" server one day and a "production" server the next.

The rest of this section covers three standard ways to move values **out** of the source code: command-line arguments, environment variables, and configuration files.

---

## 1b.5.2 Command-Line Arguments

The simplest way to pass a value into a script is via `sys.argv`, a list of the raw strings passed on the command line — but for anything beyond one or two positional values, the standard library's **`argparse`** module is the idiomatic choice: it handles parsing, default values, type conversion, and even generates a `--help` message automatically.

```python
import argparse

parser = argparse.ArgumentParser(description="Process IoT sensor data.")
parser.add_argument("--device-id", type=str, required=True, help="ID of the device to process")
parser.add_argument("--threshold", type=float, default=25.0, help="Temperature threshold")

# In a real script this would just be parser.parse_args() (reading sys.argv);
# a list is passed explicitly here only to simulate command-line input for this example.
args = parser.parse_args(["--device-id", "sensor_1", "--threshold", "30"])

print(args.device_id, args.threshold)
```

**Output:**
```
sensor_1 30.0
```

Run for real from a terminal, this same script would be invoked as:

```
python3 process_sensor.py --device-id sensor_1 --threshold 30
```

**Key Points:**
- `argparse` automatically converts types (`--threshold 30` becomes the `float` `30.0`, per `type=float`), validates required arguments, and produces a `--help` message for free.
- Values passed this way never need to be edited into the source code itself.

---

## 1b.5.3 Environment Variables

An **environment variable** is not a Python concept at all — it is an **operating system** concept. Every running process (a shell session, a program, a script) is given its own small set of key-value string pairs by the OS, called its **environment**. This is completely separate from any file on disk, and separate from the variables defined inside a program's own code.

The key mechanism is **inheritance**: when a process starts another process (e.g., a terminal launching `python3 my_script.py`), the operating system copies its current environment into the new process. So if a value is `export`ed in the shell *before* running a Python script, that value is already present in the script's environment the moment it starts — nothing had to be read from a file, and nothing had to be passed explicitly on the command line.

This can be seen directly from a shell, with no Python involved at all:

```
$ export API_KEY=abc123
$ echo $API_KEY
abc123
$ env | grep API_KEY
API_KEY=abc123
```

`export API_KEY=abc123` sets the variable in the shell's own environment; `echo $API_KEY` reads it back; `env` lists every environment variable the current process has (inherited from its parent, plus anything set explicitly). A Python script launched from this same shell would inherit `API_KEY` automatically, before a single line of Python code runs.

Python's `os` module (`os.environ`, `os.getenv()`) does not create or store this data itself — it simply reads the same OS-level environment that was already attached to the process when it started, the same one `env` prints above. They are the standard way to pass configuration (and especially secrets) that should never be committed to source control.

```python
import os

# Normally set outside Python (e.g. `export API_KEY=...` in the shell, or by the deployment
# platform); set here directly only so this example is self-contained.
os.environ["API_KEY"] = "demo-key-12345"

api_key = os.getenv("API_KEY")
missing = os.getenv("NOT_SET", "default-value")   # falls back to the given default

print(api_key)
print(missing)
```

**Output:**
```
demo-key-12345
default-value
```

For local development, it is common to keep environment variables in a `.env` file (never committed to version control) and load it with the third-party `python-dotenv` package:

```python
# .env file content:
# API_KEY=demo-key-12345

from dotenv import load_dotenv
import os

load_dotenv()   # reads the .env file and populates os.environ
api_key = os.getenv("API_KEY")
```

> This pattern — configuration read from the environment, never hardcoded — is one of the core ideas of the [**12-factor app**](https://12factor.net/config) methodology for building deployable, portable applications.

**Key Points:**
- `os.getenv(name, default)` reads an environment variable, returning `default` if it is not set.
- Environment variables are the standard place for secrets and per-deployment configuration — they live outside the code and outside version control.

---

## 1b.5.4 Configuration Files: YAML and JSON

For configuration with more structure than a handful of flat key-value pairs, a **configuration file** is usually clearer than a long list of environment variables or command-line flags. The two most common formats are quickly compared here.

**JSON** — strict, no comments allowed, needs no extra library (`json` is in the standard library):

```python
import json

config_json = '{"device_id": "sensor_1", "sample_rate_seconds": 5, "debug": false}'
config = json.loads(config_json)   # use json.load(file) to read directly from a file

print(config["device_id"], config["sample_rate_seconds"])
```

**Output:**
```
sensor_1 5
```

**YAML** — more human-friendly (supports comments, less punctuation), needs the third-party `PyYAML` package (`pip install pyyaml`):

```python
import yaml

config_yaml = """
device_id: sensor_1
sample_rate_seconds: 5   # how often to read the sensor
debug: false
"""
config = yaml.safe_load(config_yaml)   # use yaml.safe_load(file) to read directly from a file

print(config["device_id"], config["sample_rate_seconds"])
```

**Output:**
```
sensor_1 5
```

**Key Points:**
- **JSON**: strict syntax, no comments, ubiquitous (also used for APIs), no extra dependency — a good default for machine-to-machine data.
- **YAML**: more readable for humans, supports comments, requires `PyYAML` — a common choice for hand-edited configuration files.
- Always use `yaml.safe_load()`, never the plain `yaml.load()`, to avoid executing arbitrary code embedded in an untrusted YAML file.

---

# 1b.6 Logging

## 1b.6.1 Why Not Just `print()`?

**Logging** is the practice of having a running program record what it is doing — as a sequence of timestamped messages — so that someone can look back at that record later to understand what happened, diagnose a problem, or simply confirm that things are working as expected. The idea is not specific to Python, or even to programming languages with a dedicated `logging`-style library: it is a general practice, present in essentially every serious piece of software.

A central part of logging is that **not every message matters equally**. Logging systems organize messages into **severity levels**, from least to most serious:

- **DEBUG** — fine-grained detail, useful only while actively developing or troubleshooting (e.g. "entering function X with these arguments").
- **INFO** — confirmation that something expected happened (e.g. "device sensor_1 started").
- **WARNING** — something unexpected happened, but the program can keep going (e.g. "battery level low").
- **ERROR** — something failed, and a specific operation could not complete (e.g. "failed to reach the device").
- **CRITICAL** — a severe failure, possibly threatening the program's ability to keep running at all (e.g. "cannot connect to the database at startup").

Attaching a level to every message is what makes it possible to **filter** later: during normal operation you might only want to see `WARNING` and above, while actively chasing a bug you might want to see everything, `DEBUG` included — without changing a single logging call in the code, only the configured threshold (Section 1b.6.2 shows exactly how).

With this in mind, it is easier to see why `print()`, used on its own, is not a substitute for real logging:

- No **severity levels** — every `print()` call looks identical; there is no built-in way to tell "just informational" from "critical failure" apart from reading the message itself, and no way to filter by importance.
- No **timestamps** by default.
- Cannot easily be **filtered** (e.g., "show me only warnings and above") or **redirected** (e.g., "send this to a file, not the terminal") without extra, manual work.
- No **persistent record** — once the terminal closes, the output is gone.

## 1b.6.2 The `logging` Module Basics

Python's built-in `logging` module solves all of the above, implementing exactly the general idea from Section 1b.6.1: messages, each with one of the five severity levels just introduced, filterable by a configured threshold.

```python
import logging

logging.basicConfig(level=logging.INFO, format="%(levelname)s: %(message)s")
logger = logging.getLogger(__name__)

logger.debug("This won't show, level is INFO")
logger.info("Device sensor_1 started")
logger.warning("Battery level low: 15%")
logger.error("Failed to reach the device")
```

**Output:**
```
INFO: Device sensor_1 started
WARNING: Battery level low: 15%
ERROR: Failed to reach the device
```

- `logging.basicConfig(level=logging.INFO, ...)` sets the **minimum level** that gets shown — here, `DEBUG` messages are filtered out entirely, which is why the first call produces no output.
- `logging.getLogger(__name__)` is the standard pattern: it names the logger after the current module, so log messages from different files can be told apart.
- The `format` string controls what each log line looks like; a common addition is `%(asctime)s` at the front, to include a timestamp.

## 1b.6.3 Logging to a File

Instead of (or in addition to) printing to the terminal, a **handler** can send log records elsewhere — most commonly, to a file:

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(message)s",
    filename="smart_home.log",
)
logger = logging.getLogger(__name__)
logger.info("Device sensor_1 started")   # written to smart_home.log, not printed to the terminal
```

**Key Points:**
- `logging` replaces scattered `print()` calls with leveled, filterable, timestamped, redirectable messages.
- `logger.debug/info/warning/error/critical(...)` log at increasing severity; `basicConfig(level=...)` sets the cutoff.
- A `filename=...` argument to `basicConfig` is the simplest way to send log output to a file instead of the terminal.

---

# 1b.7 Managing Dependencies

Almost every real project depends on third-party packages (e.g. `PyYAML`, used in Section 1b.5.4). **Dependency management** means declaring, somewhere in the project, exactly which packages — and which versions of them — are needed, so that anyone (a teammate, a server, a CI pipeline) can recreate the same working environment with one command, instead of guessing what to `pip install`.

Python has two standard ways to declare this, covered as two subsections below: the older, simpler **`requirements.txt`**, and the modern, more complete **`pyproject.toml`**. They are not mutually exclusive — many projects use both, each for what it is best at, as explained at the end of this section.

## 1b.7.1 `requirements.txt`

A `requirements.txt` file lists the third-party packages a project depends on, one per line.

```
# requirements.txt
PyYAML==6.0.3
python-dotenv==1.0.1
requests>=2.31.0
```

- **`pip freeze > requirements.txt`** generates this file automatically, listing every package currently installed in the active environment, pinned to its exact installed version.
- **`pip install -r requirements.txt`** installs everything listed, recreating the same environment elsewhere.

**Version pinning: what `==`, `>=`, and the other operators mean**

Each line in `requirements.txt` can constrain *which* versions of a package are acceptable, using comparison operators very similar to the ones used to compare numbers in Python itself. Given a project that depends on `requests`:

| Line in `requirements.txt` | Meaning | Would accept | Would reject |
|---|---|---|---|
| `requests==2.31.0` | **Exactly** this version, nothing else | `2.31.0` | `2.31.1`, `2.30.0`, `2.32.0` |
| `requests>=2.31.0` | This version **or any newer one** | `2.31.0`, `2.32.0`, `3.0.0` | `2.30.0` |
| `requests<=2.31.0` | This version **or any older one** | `2.31.0`, `2.30.0` | `2.32.0` |
| `requests>2.31.0` | **Strictly newer** than this version | `2.31.1`, `2.32.0` | `2.31.0` (excluded), `2.30.0` |
| `requests<2.31.0` | **Strictly older** than this version | `2.30.0` | `2.31.0` (excluded), `2.32.0` |
| `requests!=2.31.0` | Any version **except** this one | `2.30.0`, `2.32.0` | `2.31.0` |
| `requests~=2.31.0` | "**Compatible release**": this version or newer, but only patch-level updates | `2.31.0`, `2.31.7` | `2.32.0`, `3.0.0` |

A few points worth making concrete:

- `==` is the strictest: it locks the dependency to one single, specific version — the most **reproducible** choice (everyone who installs it gets byte-for-byte the same package), but it means someone has to manually bump the number to ever receive a bug fix or security patch.
- `>=` is the most permissive of the simple operators: it only sets a *floor*, with no ceiling at all — a `2.31.0` requirement written as `>=2.31.0` would happily accept a hypothetical, potentially incompatible `5.0.0` release two years later.
- `~=` (the "compatible release" operator) is a middle ground used specifically to avoid that last problem: `requests~=2.31.0` behaves like `>=2.31.0` **and** `<2.32.0` combined — it tracks bug-fix updates (`2.31.1`, `2.31.2`, ...) automatically, but refuses to jump to a new minor version (`2.32.0`) that might change behavior.
- Constraints can also be **combined** with a comma, which is common in practice as a safer middle ground than a bare `>=`: `requests>=2.28,<3.0` means "at least `2.28`, but strictly less than `3.0`."

Which operator to prefer depends on the project: applications deployed to production usually pin with `==` for maximum reproducibility; libraries meant to be reused by other projects usually declare a range (`>=`, `~=`, or a combined bound) so they do not needlessly conflict with whatever versions the projects depending on them already use.

**Key Points:**
- `requirements.txt` makes a project's dependencies explicit and reproducible on any machine.
- `pip freeze` writes it; `pip install -r` reads it.
- It only ever lists dependencies — nothing about the project itself (its name, its version, how to build or install it as a package). That gap is exactly what `pyproject.toml`, covered next, fills.

## 1b.7.2 `pyproject.toml`

`pyproject.toml` is the modern, standardized configuration file for a Python project. Unlike `requirements.txt`, it is not limited to listing dependencies: it is a single place for the project's **metadata** (name, version, description, authors), its **dependencies**, and instructions for **packaging tools** on how to actually build and install it.

```toml
# pyproject.toml
[project]
name = "smart-home-system"
version = "0.1.0"
description = "A simple Smart Home IoT management system"
authors = [{ name = "Prof. Marco Picone", email = "marco.picone@unimore.it" }]
requires-python = ">=3.10"
dependencies = [
    "PyYAML>=6.0,<7.0",
    "python-dotenv~=1.0",
]

[build-system]
requires = ["setuptools>=68.0"]
build-backend = "setuptools.build_meta"
```

- The **`[project]`** table holds the metadata and the `dependencies` list — the same information `requirements.txt` held, using the exact same version operators from Section 1b.7.1 (`>=6.0,<7.0`, `~=1.0`, ...), just nested inside one structured file instead of a separate plain-text one.
- The **`[build-system]`** table tells packaging tools *how* to build this project into an installable package, and with what tool (here, `setuptools`) — this is the piece `requirements.txt` has no equivalent for at all.

With a `pyproject.toml` in place, the project itself becomes installable as a package:

```
pip install .        # regular install: copies the package into site-packages
pip install -e .     # editable/"development" install: links to the source instead of copying it,
                      # so local edits are picked up immediately without reinstalling
```

> The `-e` (editable) install is exactly what makes the `src/` layout from Section 1b.9 practical during development: it installs the package properly — closing the "silently imports the local, uninstalled folder" gap discussed there — while still letting the code be edited in place.

Higher-level tools such as [`build`](https://build.pypa.io/) (to produce a distributable package), or all-in-one project managers like `poetry`, `hatch`, or `uv`, all read and write `pyproject.toml` as their common, standardized format — this is precisely why it has become the standard, replacing the older `setup.py`.

**When to use which:**

| | `requirements.txt` | `pyproject.toml` |
|---|---|---|
| **Best for** | A quick, pinned snapshot of exact versions to reproduce an environment | A project meant to be built, installed, or distributed as an actual package |
| **Contains** | Only a dependency list | Project metadata + dependencies + build instructions |
| **Typical use** | `pip install -r requirements.txt` on a server, in CI, or in a Docker image | `pip install .` / `pip install -e .`, or building a distributable package with `build` |
| **A simple script or notebook project** | ✅ Usually enough on its own | Not needed unless it will be packaged |
| **A reusable library, or an application with an installable CLI** | Not enough on its own | ✅ The standard choice |

In practice, the two are often combined rather than treated as strictly either/or: a project declares its dependencies once in `pyproject.toml` (the source of truth for anything meant to be installed), and may still export a pinned `requirements.txt` — via `pip freeze`, or an equivalent "lock file" from a tool like `poetry` or `uv` — specifically for maximally reproducible deployments, where even transitive dependencies need to be pinned exactly.

---

# 1b.8 Virtual Environments

## 1b.8.1 What Is a Virtual Environment, and Why It Matters

By default, there is only **one** Python installation on a machine (the "system" or "global" Python), and every package installed with `pip` goes into that same, shared installation — used by every script and every project on that machine, all at once. This causes a very concrete problem: if **Project A** needs `requests==2.20.0` and **Project B**, developed later on the same machine, needs `requests>=2.31.0`, there is no way to satisfy both at the same time — installing one version necessarily means the other project stops working, because there is only one shared copy of `requests` for the whole system to share.

A **virtual environment** solves this by giving each project its **own private copy** of the Python interpreter and its own private package directory, completely isolated from the system Python and from every other virtual environment. Activating a project's virtual environment simply makes `python` and `pip` point at that private copy instead of the shared, global one — so `pip install` only ever affects the one project currently active.

This matters for several concrete reasons:
- **Isolation**: two projects on the same machine can depend on different, even incompatible, versions of the same package without conflict.
- **Reproducibility**: a virtual environment built from a project's `requirements.txt` or `pyproject.toml` (Section 1b.7) contains *exactly* those dependencies — nothing extra left over from unrelated work on the same machine.
- **No special privileges needed**: a virtual environment is just a regular, user-owned directory, so installing packages into it never requires administrator/root access.
- **Safe to throw away**: since it holds nothing but reinstallable packages, a broken or messy virtual environment can simply be deleted and recreated from scratch, instead of being carefully repaired.

## 1b.8.2 Creating and Using a Virtual Environment with `venv`

Python includes a built-in module, `venv`, for exactly this purpose — no extra installation required.

```
$ python3 -m venv venv
```

This creates a `venv/` directory (the name `venv` is a common convention, though any name works) containing a private copy of the interpreter and an empty package directory.

**Choosing which Python version goes into the environment.** `venv` itself has no "version" option — the version that ends up inside the environment is simply whichever interpreter is used to *run* the `-m venv` command. If a machine has several Python versions installed side by side, each reachable under its own command (`python3.11`, `python3.12`, ...), the version is picked by invoking that specific one:

```
$ python3.11 -m venv venv       # creates an environment using Python 3.11
$ python3.12 -m venv venv       # creates a *different* environment using Python 3.12
```

This only works with a version **already present on the machine** — `venv` does not download or install anything, it only wraps an interpreter that already exists. Getting an additional Python version onto a machine in the first place is a separate task, handled by the operating system's package manager, the official installers from [python.org](https://www.python.org/downloads/), or a dedicated version manager such as `pyenv` (or newer all-in-one tools like `uv`, which can install Python versions on demand).

Before an environment is **activated**, `python3` still refers to the system interpreter:

```
$ which python3
/usr/bin/python3
```

**Activating** the environment (the exact command differs by OS) switches `python3` and `pip` to point at the private copy instead:

```
$ source venv/bin/activate        # macOS / Linux
> venv\Scripts\activate           # Windows

(venv) $ which python3
/path/to/project/venv/bin/python3
```

From this point on, every `pip install` only affects this one environment. A freshly created environment starts essentially empty:

```
(venv) $ pip list
Package Version
------- -------
pip     25.1.1
```

Running `deactivate` at any point returns to the system Python:

```
(venv) $ deactivate
$ which python3
/usr/bin/python3
```

**Isolation, made concrete**: creating two separate environments and installing something in only one of them shows the isolation directly — the second environment is completely unaffected:

```
$ python3 -m venv venv_a && python3 -m venv venv_b

$ source venv_a/bin/activate
(venv_a) $ pip install requests==2.31.0
(venv_a) $ pip list
Package            Version
------------------ ---------
certifi            2026.7.22
charset-normalizer 3.5.1
idna               3.19
pip                25.1.1
requests           2.31.0
urllib3            2.7.0
(venv_a) $ deactivate

$ source venv_b/bin/activate
(venv_b) $ pip list
Package Version
------- -------
pip     25.1.1
```

`requests` (and its own dependencies, pulled in automatically) exists only inside `venv_a`. `venv_b`, created independently, never sees it — exactly as if the two environments lived on two entirely separate machines.

## 1b.8.3 Virtual Environments and Dependency Management, Together

Virtual environments and the dependency files from Section 1b.7 are meant to be used **together**, not as alternatives to each other: the dependency file (`requirements.txt` or `pyproject.toml`) is the reproducible **recipe**; the virtual environment is the disposable **kitchen** where that recipe gets prepared, fresh, every time. The typical workflow combines both:

```
$ python3 -m venv venv
$ source venv/bin/activate
(venv) $ pip install -r requirements.txt      # or: pip install -e .   for a pyproject.toml-based project
```

Once new packages are added during development, the dependency file is updated to match — e.g. `pip freeze > requirements.txt` — so the *declared* dependencies and the *installed* ones never drift apart.

**Best practices**, following the recommendations from [Real Python's Virtual Environments reference](https://realpython.com/ref/best-practices/virtual-environments/):

- **Always activate the right environment before installing anything.** Installing a package with no environment activated silently falls back to the system Python — exactly the shared, global installation virtual environments exist to avoid.
- **Treat a virtual environment as disposable, not something to repair.** If it ends up in a broken or confusing state, the standard fix is to delete the directory entirely and recreate it from the dependency file — this is precisely why keeping that file accurate matters so much.
- **Never commit the virtual environment folder to version control.** It can always be regenerated from `requirements.txt`/`pyproject.toml`, is often large, and is specific to one machine's OS and file paths — only the dependency *declarations* belong in version control, not the environment built from them. (Since Python 3.13, `venv` automatically writes a `.gitignore` file inside the new environment for exactly this reason.)

**Key Points:**
- A virtual environment gives a project its own private, isolated copy of the Python interpreter and installed packages.
- `python3 -m venv venv` creates one; `source venv/bin/activate` (or `venv\Scripts\activate` on Windows) activates it; `deactivate` returns to the system Python.
- The Python **version** inside the environment is whichever interpreter created it (`python3.11 -m venv ...` vs `python3.12 -m venv ...`) — it must already be installed on the machine, `venv` cannot install a new version by itself.
- It solves dependency **conflicts between projects**, not dependency **declaration** — that is still the job of `requirements.txt`/`pyproject.toml` (Section 1b.7); the two are companions, used together.
- Never commit a virtual environment folder to version control — only the dependency declaration files that can recreate it.

---

# 1b.9 Python Project Layout

Unlike some languages and frameworks, Python itself does not enforce any particular project structure. There is no compiler or build tool that rejects a `.py` file for being in the "wrong" place, and a script can import anything from anywhere, as long as Python can find it (Section 1b.10 covers exactly how). This is different from, say, a Django or Flask project scaffolded with the framework's own command-line tool, which imposes a specific, opinionated layout from the very start — the Python interpreter itself has no such opinion, with or without a framework.

This freedom is a double-edged sword: a project can grow with no upfront ceremony, but nothing stops it from turning into an unstructured pile of files either, imported in inconsistent, hard-to-follow ways. Giving a project deliberate structure is therefore a matter of **discipline**, not of following a language rule — but it is just as important as any other practice covered in this lecture. How the files of a Python project are organized affects how easy it is to navigate, test, package, and eventually distribute — for the same underlying reasons already discussed for naming (Section 1b.4.2), configuration (Section 1b.5), and dependencies (Section 1b.7): a project that anyone can pick up, understand, and run the same way, without having to guess. Two layouts are common.

**Flat layout** — the package's source files sit directly at the project root:

```
project_name/
├── project_name/
│   ├── __init__.py
│   └── module1.py
├── tests/
│   └── test_module1.py
├── README.md
└── requirements.txt
```

**`src/` layout** — the package lives one level deeper, inside a dedicated `src/` directory:

```
project_name/
├── src/
│   └── project_name/
│       ├── __init__.py
│       ├── __main__.py
│       ├── module1.py
│       └── module2.py
├── tests/
│   ├── __init__.py
│   └── test_module1.py
├── LICENSE
├── README.md
└── pyproject.toml
```

For any project that will be tested, installed, or distributed as a package (not just a one-off script), the **`src/` layout is the generally recommended default** ([Real Python — Project Layout](https://realpython.com/ref/best-practices/project-layout/)). The reason is subtle but important: with a flat layout, running tests or importing the package from the project root can silently succeed by importing the **local, uninstalled source directory**, hiding packaging mistakes that would only surface once the project is actually installed elsewhere. The `src/` layout removes that shortcut — the package can only be imported once it has actually been installed (even in editable/development mode), so tests exercise the project the same way a real user eventually will.

**A more realistic example: a Smart Home project**

The examples above only show one or two module files, which does not really show *why* structure matters. Once a project grows — as the Smart Home case study from Lecture 2 realistically would, with more devices, more automation logic, and more ways for a user to interact with it — grouping files by **responsibility** into subfolders, rather than dropping everything into one flat directory, is what keeps it navigable:

```
smart_home_system/
├── src/
│   └── smart_home_system/
│       ├── __init__.py
│       ├── __main__.py
│       ├── devices/                  # what a device IS: Device, Sensor, Actuator (Lecture 2)
│       │   ├── __init__.py
│       │   ├── sensors.py            #   TemperatureSensor, HumiditySensor
│       │   └── actuators.py          #   SmartLight, SmartLock
│       ├── logic/                    # what the system DOES with device data
│       │   ├── __init__.py
│       │   └── automation_rules.py   #   e.g. "if temperature > threshold, turn on the fan"
│       ├── storage/                  # how device data is persisted and retrieved
│       │   ├── __init__.py
│       │   └── data_manager.py       #   the DataManager class from Lecture 2
│       └── interface/                # how a human or another system talks to it
│           ├── __init__.py
│           └── cli.py                #   a command-line interface, for now
├── tests/
│   ├── __init__.py
│   ├── test_automation_rules.py
│   └── test_data_manager.py
├── config/
│   └── settings.yaml                 # Section 1b.5.4
├── LICENSE
├── README.md
└── pyproject.toml
```

Every subfolder answers a different question about the code inside it:

- **`devices/`** — *what a device is*: the `Device`/`Sensor`/`Actuator` class hierarchy from Lecture 2, with no awareness of automation rules or storage.
- **`logic/`** — *what the system decides to do*: the rules that react to device data (e.g., turning on a light when a room is dark), built on top of `devices/` but independent of how data happens to be stored or displayed.
- **`storage/`** — *how device data is kept and retrieved*: exactly the responsibility the `DataManager` class already had in Lecture 2 (Section 2.14) — swapping it from in-memory storage to a real database would only mean changing files inside this one folder.
- **`interface/`** — *how the outside world interacts with the system*: a CLI today; in later lectures (Section 4, HTTP/REST), this could just as easily become a web API instead, without touching `devices/`, `logic/`, or `storage/` at all.

This is the same **separation of concerns** already introduced with the `SmartHome`/`DataManager` delegation in Lecture 2 (Section 2.14) — only now applied to an entire project's folder structure, not just to two classes.

**Key Points:**
- Keep top-level "housekeeping" files (`README.md`, `LICENSE`, `pyproject.toml` or `requirements.txt`) at the project root; keep source code grouped into directories rather than loose files.
- A `tests/` directory, mirroring the package's structure, is standard practice.
- The `src/` layout is the safer default for anything meant to be installed or distributed, precisely because it prevents accidentally importing an uninstalled local copy of the package.
- As a project grows, subfolders should split code by **responsibility** (e.g. `devices/`, `logic/`, `storage/`, `interface/`), not by convenience — the same separation-of-concerns idea already used for the `SmartHome`/`DataManager` delegation in Lecture 2, just applied at the scale of a whole project.

---

# 1b.10 Python Modules & Packages

## 1b.10.1 Modules and `import`

A **module** is simply a single `.py` file. Anything defined in it — functions, variables, classes — becomes accessible from other files via `import`.

```python
# sensors.py
def read_temperature():
    return 21.5
```

```python
# main.py
import sensors
print(sensors.read_temperature())

# or, to import a specific name directly:
from sensors import read_temperature
print(read_temperature())
```

## 1b.10.2 Packages and `__init__.py`

A **package** is a directory containing multiple modules, marked as a package by an `__init__.py` file inside it (which can be empty — its mere presence is what historically made the directory importable as a package; this file is also the natural place to run package-level setup code, or to decide what a `from package import *` should expose).

```
smart_home/
├── __init__.py
├── sensors.py
└── utils.py
```

```python
# smart_home/sensors.py
def read_temperature():
    return 21.5
```

```python
# main.py, at the project root, next to the smart_home/ directory
from smart_home.sensors import read_temperature
print(read_temperature())
```

**Output:**
```
21.5
```

**When does `__init__.py` actually matter?** Modern Python (3.3+) can technically import a directory as a so-called **namespace package** even with no `__init__.py` inside it at all — the earlier example still runs without it. This makes it easy to conclude the file is now optional and skip it. It is not: skipping it removes a safety net, and the way it fails is exactly the kind of *silent*, hard-to-trace problem this lecture has been warning about since Section 1b.4.

Here is the concrete problem. Two **completely unrelated** projects both happen to have a top-level folder named `smart_home` — one belonging to `project_a`, one to `project_b`. Neither has an `__init__.py`:

```python
import sys
sys.path.insert(0, "project_a")
sys.path.insert(0, "project_b")

from smart_home.sensors import read_temperature      # lives in project_a/smart_home/
from smart_home.actuators import turn_on_light        # lives in project_b/smart_home/

print(read_temperature())
print(turn_on_light())
```

**Output:**
```
21.5
light is ON
```

Both imports **succeed**, as if `project_a/smart_home` and `project_b/smart_home` were one single package — Python silently merges any same-named, `__init__.py`-less directories it finds across `sys.path` into a combined namespace. Two unrelated pieces of code, from two unrelated projects, end up mixed together with no error and no warning at all.

Adding `__init__.py` to `project_a/smart_home` changes this completely:

```python
# same code as above, but project_a/smart_home now has an __init__.py

print(read_temperature())          # 21.5 -> still works

from smart_home.actuators import turn_on_light   # actuators.py is in project_b's smart_home
```

**Output:**
```
21.5
Traceback (most recent call last):
  ...
ModuleNotFoundError: No module named 'smart_home.actuators'
```

The moment `smart_home` becomes a **regular** package (via `__init__.py`), Python stops merging it with anything else on `sys.path` — `project_b`'s `actuators.py` becomes invisible, and trying to import it fails **loudly and immediately**, with a clear error pointing at the exact problem, instead of quietly mixing unrelated code together.

> **A simple rule of thumb for this course:** always add an `__init__.py` file — even an empty one — to every directory meant to be imported as a package. Treat namespace packages (no `__init__.py`) as an advanced, special-purpose feature to reach for deliberately, not a shortcut for skipping a file that "seems unnecessary." An empty `__init__.py` costs nothing and guarantees Python will fail clearly and immediately if something is wrong, instead of silently doing something unexpected.

## 1b.10.3 Absolute vs. Relative Imports

An **absolute import** spells out the full path to what is being imported, starting from a top-level package — this is what `main.py` used above: `from smart_home.sensors import read_temperature`.

A **relative import**, used only *from within* a package, refers to sibling modules using leading dots: a single `.` means "this same package," `..` means "the parent package," and so on.

```python
# smart_home/utils.py
from .sensors import read_temperature   # relative import: "from this same package"

def read_temperature_fahrenheit():
    celsius = read_temperature()
    return celsius * 9 / 5 + 32


if __name__ == "__main__":
    print(read_temperature_fahrenheit())
```

Relative imports only work when Python knows the file is being run **as part of a package** — a common pitfall is running such a file directly, versus running it *as a module* with the `-m` flag:

```
$ python3 smart_home/utils.py
Traceback (most recent call last):
  ...
ImportError: attempted relative import with no known parent package

$ python3 -m smart_home.utils
70.7
```

Run directly (`python3 smart_home/utils.py`), Python has no idea `utils.py` belongs to the `smart_home` package, so `from .sensors import ...` fails immediately. Run as a module (`python3 -m smart_home.utils`), Python resolves `smart_home` as the package `utils` lives in, and the same relative import works.

**Key Points:**
- A **module** is one `.py` file; a **package** is a directory of modules, marked with `__init__.py`.
- **Absolute imports** (`from smart_home.sensors import ...`) spell out the full path and work from anywhere the top-level package is importable — the safer default in most code.
- **Relative imports** (`from .sensors import ...`) are shorter but only valid inside a package, and only when Python is aware of that package context — which is why running a file directly, instead of with `python3 -m package.module`, is a classic source of `ImportError`.

---

# 1b.11 Faster Project & Dependency Management with `uv`

## 1b.11.1 What Is `uv`?

Sections 1b.7 and 1b.8 covered `pip`, `requirements.txt`, `pyproject.toml`, and `venv` as separate, standard-library-adjacent tools, each solving one piece of the puzzle. [**`uv`**](https://docs.astral.sh/uv/) is a single, modern command-line tool (built by Astral, written in Rust for speed) that **combines all of those roles** — package installer, virtual environment manager, and project/build tool — while still producing and reading the exact same `pyproject.toml` already introduced in Section 1b.7.2. It is not a competing standard; it is a faster, more convenient way to do the same things covered so far.

Installation instructions change over time and depend on the operating system, so rather than duplicating them here, follow the official installation guide: **https://docs.astral.sh/uv/getting-started/installation/**. The rest of this section assumes `uv` is already installed and available as the `uv` command.

## 1b.11.2 Initializing a Project with the `src/` Layout Already Covered

`uv init --package` scaffolds a new project using exactly the `src/` layout from Section 1b.9, instead of it having to be built by hand:

```
$ uv init --package smart_home_system
Initialized project `smart-home-system`
```

**Output (files created):**
```
smart_home_system/
├── .gitignore              # already excludes .venv/ - Section 1b.8.3's rule, enforced from the start
├── .python-version         # pins the Python version for this project
├── README.md
├── pyproject.toml
└── src/
    └── smart_home_system/
        └── __init__.py     # contains a starter main() function
```

The generated `pyproject.toml` already has the `[project]` and `[build-system]` tables from Section 1b.7.2 filled in:

```toml
[project]
name = "smart-home-system"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
authors = [
    { name = "Your Name", email = "you@example.com" }
]
requires-python = ">=3.12"
dependencies = []

[project.scripts]
smart-home-system = "smart_home_system:main"

[build-system]
requires = ["uv_build>=0.11.29,<0.12.0"]
build-backend = "uv_build"
```

From here, the richer, multi-folder layout from Section 1b.9 (`devices/`, `logic/`, `storage/`, `interface/`) is simply built by hand inside `src/smart_home_system/`, exactly as shown there — `uv init` provides the standard scaffolding around it, not a replacement for it.

## 1b.11.3 Dependencies and the Virtual Environment, Handled Automatically

This is where `uv` most visibly merges Section 1b.7 (dependencies) and Section 1b.8 (virtual environments) into one step. Adding a dependency does not require creating a virtual environment first — `uv` does it automatically the first time it is needed:

```
$ cd smart_home_system
$ uv add pyyaml
Using CPython 3.12.13
Creating virtual environment at: .venv
Resolved 2 packages in 81ms
Installed 2 packages in 7ms
 + pyyaml==6.0.3
 + smart-home-system==0.1.0 (from file:///.../smart_home_system)
```

A single command: created `.venv` (Section 1b.8.2), installed `pyyaml` into it, added `pyyaml` to the `dependencies` list in `pyproject.toml`, and generated a `uv.lock` file recording the *exact* resolved versions of every dependency (including transitive ones) — an even stronger, automatically maintained version of the "pinned `requirements.txt` for reproducibility" idea from Section 1b.7.1.

Rebuilding the environment from scratch — the "disposable kitchen" idea from Section 1b.8.3 — becomes a single command instead of a multi-step `venv` + `pip install -r` sequence:

```
$ rm -rf .venv
$ uv sync
Creating virtual environment at: .venv
Resolved 2 packages in 0.94ms
Installed 2 packages in 4ms
 + pyyaml==6.0.3
 + smart-home-system==0.1.0 (from file:///.../smart_home_system)
```

## 1b.11.4 Running One or More `main`s from the Command Line

`uv run` executes a command **inside the project's `.venv`** automatically — there is no need to `source .venv/bin/activate` first (Section 1b.8.2); `uv` locates the project's environment and uses it directly:

```
$ uv run python -c "import sys; print(sys.executable)"
/.../smart_home_system/.venv/bin/python3
```

The project's own entry point (declared under `[project.scripts]` in `pyproject.toml` above) can be run directly by name:

```
$ uv run smart-home-system
Hello from smart-home-system!
```

A real project usually has **more than one** runnable entry point — following the multi-folder layout from Section 1b.9, imagine `src/smart_home_system/interface/cli.py` with its own `main()`:

```python
# src/smart_home_system/interface/cli.py
def main() -> None:
    print("Smart Home CLI starting...")


if __name__ == "__main__":
    main()
```

`uv run` can execute it two equivalent ways — as a module (Section 1b.10.1), or by file path directly:

```
$ uv run python -m smart_home_system.interface.cli
Smart Home CLI starting...

$ uv run src/smart_home_system/interface/cli.py
Smart Home CLI starting...
```

Both run inside the same project `.venv`, with `pyyaml` and every other declared dependency already available — no separate activation step, regardless of which one of the project's several entry points is being run.

**Key Points:**
- `uv` is not a new concept — it implements everything from Sections 1b.7 and 1b.8 (dependencies, `pyproject.toml`, virtual environments) in one faster tool, using the same standard `pyproject.toml` format.
- `uv init --package` scaffolds the `src/` layout from Section 1b.9 automatically.
- `uv add <package>` installs a dependency **and** creates/updates the `.venv` in one step; `uv sync` rebuilds that `.venv` from `pyproject.toml`/`uv.lock` on demand.
- `uv run <target>` runs any script, module (`-m package.module`), or declared `[project.scripts]` entry point inside the project's `.venv`, without a manual `activate` step — the natural way to run one of several `main`s in a multi-module project.
