# Python Best Practices, Object Oriented Programming & Use Case Modelling

<!-- omit in toc -->
## Lecture Information

| **Master's Degree** | Intelligent Internet of Things (D.M.270/04)                                      |
|---------------------|----------------------------------------------------------------------------------|
| **Course**          | Intelligent Internet of Things                                                   |
| **Lecture Title**   | Python Best Practices, Object Oriented Programming (OOP) & Use Case Modelling    |
| **Author**          | Prof. Marco Picone (marco.picone@unimore.it)                                     |
| **License**         | [Creative Commons Attribution 4.0](https://creativecommons.org/licenses/by/4.0/) |

---

## 📚 About This Repository

This repository collects the lecture notes for the **Python & OOP** module of the
*Intelligent Internet of Things* course. 

The material is written as long-form Markdown notes with runnable code snippets,
diagrams, and a running **Smart Home IoT** case study that is modelled and
implemented step by step. The goal is to move from *"a collection of scripts"* to a
maintainable, object-oriented piece of software — using an IoT scenario as the
common thread.

## 🗂️ How It Is Organised

The content is split into **two standalone lecture files**, meant to be read in
order:

### 1️⃣ [`python_best_practices.md`](python_best_practices.md) — *Lecture 1b · Python Best Practices*

Practices that make *any* Python code more robust, readable, and maintainable,
regardless of whether it uses OOP. It covers:

- What `if __name__ == "__main__":` actually does, and why it matters
- Exception handling (`try` / `except` / `else` / `finally`, custom exceptions)
- Python data types and type hints — and what goes wrong without them
- Getting rid of hardcoded values: CLI arguments, environment variables, YAML/JSON config files
- Logging as a replacement for scattered `print()` calls
- Dependency management with `requirements.txt` and `pyproject.toml`
- Virtual environments with `venv`, and how they pair with dependency management
- Python project layout, modules, packages, and absolute vs. relative imports
- `uv` as a modern, faster all-in-one project/dependency/venv workflow

### 2️⃣ [`python_oop.md`](python_oop.md) — *Lecture 2 · Python OOP & Use Case Modelling*

Object-Oriented Programming from first principles, applied to an IoT use case:

- OOP introduction: paradigms, procedural vs. OO, the four pillars
- OOP in Python: classes, `__init__`, `self`, instance/class/static members, dunder methods
- Encapsulation and access control, getters/setters, `@property`
- Inheritance, method overriding, multiple inheritance, MRO
- Polymorphism (duck typing, operator, class-based) and abstraction with `abc`
- Exception management in an OOP context
- **Smart Home example**: identifying entities, sensors & actuators, refactoring the model with inheritance (`Device` → `Sensor` / `Actuator` → `TemperatureSensor`, `HumiditySensor`, `SmartLight`)
- 🗃️ Smart Home + Data Manager, and implementing the `SmartHome` class and its behaviours
- 🎯 Design patterns: Singleton, Factory, Observer, and the delegation principle

## 📁 Repository Layout

```
.
├── python_best_practices.md   # Lecture 1b - Python Best Practices
├── python_oop.md              # Lecture 2 - Python OOP & Use Case Modelling
├── images/                    # Diagrams and figures referenced by the notes
├── LICENSE                    # Creative Commons Attribution 4.0
└── README.md                  # This file
```

## 📄 License

This work is released under the
[Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/)
license. You are free to share and adapt the material, provided you give
appropriate credit to the author.
