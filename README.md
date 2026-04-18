# 🔗 pyobj.h


<div align="center">

[![Platform](https://img.shields.io/badge/platform-Any-blue?logo=c%2B%2B&logoColor=white)](https://isocpp.org/)
[![Language](https://img.shields.io/badge/language-C%2B%2B17-00599C?logo=c%2B%2B)](https://isocpp.org/)
[![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)

*Header-only C++17 library for seamless Python embedding — Write Python-like code in C++*

</div>

---

## 📖 Table of Contents | Оглавление

- [English](#english)
  - [📋 Overview](#-overview)
  - [✨ Features](#-features)
  - [🚀 Quick Start](#-quick-start)
  - [📚 Core Classes](#-core-classes)
  - [🔧 Global Functions](#-global-functions)
  - [📦 JSON Support](#-json-support)
  - [💡 Examples](#-examples)
  - [⚠️ Requirements](#️-requirements)

- [Русский](#русский)
  - [📋 Обзор](#-обзор)
  - [✨ Возможности](#-возможности)
  - [🚀 Быстрый старт](#-быстрый-старт)
  - [📚 Основные классы](#-основные-классы)
  - [🔧 Глобальные функции](#-глобальные-функции)
  - [📦 Поддержка JSON](#-поддержка-json)
  - [💡 Примеры](#-примеры)
  - [⚠️ Требования](#️-требования)

---

# English

## 📋 Overview

**pyobj.h** is a **single-header C++17 library** that provides **Python-like syntax** for embedding Python in C++ applications.

### What problem does it solve?

Native Python/C API is **verbose and error-prone**:

```cpp
// Traditional Python C API — painful!
PyObject* list = PyList_New(0);
PyList_Append(list, PyLong_FromLong(42));
PyObject* item = PyList_GetItem(list, 0);
long value = PyLong_AsLong(item);
Py_DECREF(item);
Py_DECREF(list);
```

**With pyobj.h — clean and intuitive:**

```cpp
// pyobj.h — writes like Python!
py::List list;
list.append(42);
long value = list[0];  // Automatic conversion!
```

### Key Design Principles

| Principle | Implementation |
|-----------|----------------|
| **RAII Memory Management** | Automatic `Py_XINCREF`/`Py_XDECREF` |
| **Operator Overloading** | `[]`, `()`, `+`, `*`, `==`, `!=`, `<`, `>`, `|`, `&`, `^`, `+=`, `*=` |
| **Implicit Conversions** | C++ types ↔ Python objects |
| **Python-like Methods** | `.append()`, `.pop()`, `.keys()`, `.upper()`, `.split()` |
| **Variadic Templates** | `operator()` accepts any number/type of arguments |
| **Formatted Strings** | `py::fstring("Hello {name}!", py::farg("name", "World"))` |

## ✨ Features

### Core Classes

| Class | Python Equivalent | Key Methods |
|-------|-------------------|-------------|
| `py::PyObj` | `object` | Base class, `operator[]`, `operator()`, `str()`, `is_*()` |
| `py::Str` | `str` | `upper()`, `lower()`, `split()`, `join()`, `find()`, `replace()` |
| `py::List` | `list` | `append()`, `pop()`, `extend()`, `sort()`, `reverse()`, `index()` |
| `py::Tuple` | `tuple` | `index()`, `count()`, `to_list()`, `operator[]` |
| `py::Set` | `set` | `add()`, `pop()`, `union_with()`, `intersection()`, `difference()` |
| `py::Dict` | `dict` | `get()`, `set()`, `pop()`, `keys()`, `values()`, `items()`, `update()` |
| `py::Function` | `callable` | `call()`, `operator()` with kwargs support |

### Global Functions

| Function | Python Equivalent | Description |
|----------|-------------------|-------------|
| `py::print(obj)` | `print()` | Print any Python object |
| `py::pprint(obj)` | `pprint.pprint()` | Pretty-print with indentation |
| `py::len(obj)` | `len()` | Get length of sequence/collection |
| `py::type(obj)` | `type()` | Get type name as string |
| `py::sorted(seq)` | `sorted()` | Return sorted list |
| `py::reversed(obj)` | `reversed()` | Return reversed sequence |
| `py::all(list)` | `all()` | True if all elements are truthy |
| `py::any(list)` | `any()` | True if any element is truthy |
| `py::map(func, list)` | `map()` | Apply function to each element |
| `py::exec(code)` | `exec()` | Execute Python code string |
| `py::eval(code)` | `eval()` | Evaluate Python expression |
| `py::run_file(path)` | `exec(open().read())` | Execute Python file |

### JSON Support (Built-in)

| Function | Description |
|----------|-------------|
| `py::json_dump(obj, filename, indent)` | Write object to JSON file |
| `py::json_dumps(obj)` | Convert object to JSON string |
| `py::json_load(filename)` | Read JSON file to Python object |
| `py::json_loads(json_string)` | Parse JSON string to Python object |

### Formatted Strings (C++ f-string equivalent)

```cpp
std::string result = py::fstring(
    "Hello {name}! You are {age} years old.",
    py::farg("name", "Alice"),
    42  // positional argument
);
// Result: "Hello Alice! You are 42 years old."
```

## 🚀 Quick Start

### 📥 Installation

**Single-header library — just copy `pyobj.h` to your project!**

```bash
wget https://raw.githubusercontent.com/vk-candpython/pyobj/main/pyobj.h
```

### 🔨 Compilation

```bash
# Find Python development headers
g++ -std=c++17 main.cpp -o main $(python3-config --includes --ldflags)
```

### 💻 Minimal Example

```cpp
#include "pyobj.h"

int main() {
    py::init_python();
    
    // Create and manipulate Python objects
    py::List my_list = {1, 2, 3, "hello"};
    my_list.append(42);
    
    py::print(my_list);  // [1, 2, 3, 'hello', 42]
    
    // Call Python functions
    py::Function len = py::eval("len");
    std::cout << "Length: " << len(my_list).str() << std::endl;
    
    py::exit_python();
    return 0;
}
```

## 📚 Core Classes

### `py::PyObj` — Universal Python Object

Base class for all Python objects with automatic reference counting.

```cpp
// Construction from C++ types
py::PyObj a = 42;           // int
py::PyObj b = 3.14;         // float
py::PyObj c = true;         // bool
py::PyObj d = "hello";      // string

// Type checking
if (a.is_str()) { /* ... */ }
if (b.is_list()) { /* ... */ }
if (c.is_callable()) { /* ... */ }

// Indexing (works for dict, list, tuple, string)
py::PyObj value = obj["key"];    // dict access
py::PyObj item = obj[0];         // list/tuple/string access

// Universal call operator
py::Function func = py::eval("print");
func("Hello", "World", py::Dict{{"end", "!\n"}});

// String representation
std::string s = obj.str();
std::cout << obj << std::endl;  // Uses repr()
```

### `py::Str` — String Operations

```cpp
py::Str s = "Hello World";

// Case operations
py::Str upper = s.upper();      // "HELLO WORLD"
py::Str lower = s.lower();      // "hello world"
py::Str title = s.title();      // "Hello World"

// Validation
bool is_digit = s.isdigit();
bool is_alpha = s.isalpha();

// Search
long pos = s.find("World");     // 6
py::Str replaced = s.replace("World", "C++");

// Split and join
std::vector<py::Str> parts = s.split(" ");  // ["Hello", "World"]
py::Str joined = py::Str("-").join(parts);  // "Hello-World"

// Length
long len = s.len();             // 11

// Concatenation
py::Str result = s + " from C++";
s += "!";

// Repetition
py::Str stars = py::Str("*") * 10;  // "**********"
```

### `py::List` — List Operations

```cpp
// Construction
py::List list = {1, 2, 3, "four", 5.0};

// Modification
list.append(42);
list.extend({6, 7, 8});
list.insert(0, "first");
list.remove("four");

// Access
py::PyObj item = list[0];
py::PyObj popped = list.pop();      // removes last
py::PyObj popped2 = list.pop(1);    // removes at index

// Search
long idx = list.index(42);
long count = list.count(1);
bool has = list.contains(3);

// Manipulation
list.reverse();
list.sort();
list.clear();

// Length
long size = list.len();

// Operations
py::List combined = list + py::List{9, 10};
list += py::List{11, 12};
py::List repeated = list * 3;
```

### `py::Tuple` — Immutable Sequence

```cpp
py::Tuple tup = {1, 2, 3, "hello"};

// Access (read-only)
py::PyObj item = tup[0];
long len = tup.len();

// Search
long idx = tup.index(2);
long count = tup.count(1);
bool has = tup.contains("hello");

// Operations
py::Tuple combined = tup + py::Tuple{4, 5};
py::Tuple repeated = tup * 2;

// Conversion
py::List as_list = tup.to_list();
```

### `py::Set` — Unordered Unique Collection

```cpp
py::Set set = {1, 2, 3, 3, 2, 1};  // {1, 2, 3}

// Modification
set.add(4);
py::PyObj popped = set.pop();
set.clear();

// Properties
long size = set.len();
bool empty = set.empty();
bool has = set.contains(2);

// Set operations
py::Set a = {1, 2, 3};
py::Set b = {2, 3, 4};

py::Set un = a | b;              // Union: {1, 2, 3, 4}
py::Set inter = a & b;           // Intersection: {2, 3}
py::Set diff = a - b;            // Difference: {1}
py::Set sym = a ^ b;             // Symmetric difference: {1, 4}

// In-place
a |= b;   // a becomes union
a &= b;   // a becomes intersection
a -= b;   // a becomes difference
a ^= b;   // a becomes symmetric difference

// Subset/superset
bool is_sub = a.issubset(b);
bool is_super = a.issuperset(b);
```

### `py::Dict` — Key-Value Mapping

```cpp
// Construction
py::Dict dict = {
    {"name", "Alice"},
    {"age", 30},
    {"scores", py::List{95, 87, 92}}
};

// Access
py::PyObj name = dict["name"];
py::PyObj age = dict.get("age");

// Modification
dict["city"] = "New York";
dict.set("country", "USA");
py::PyObj removed = dict.pop("age");

// Properties
size_t size = dict.len();
bool empty = dict.empty();
bool has = dict.contains("name");

// Views
py::List keys = dict.keys();     // ["name", "scores", "city", "country"]
py::List values = dict.values(); // ["Alice", [...], "New York", "USA"]
py::List items = dict.items();   // [("name", "Alice"), ...]

// Merging
py::Dict other = {{"email", "alice@example.com"}};
dict.update(other);

py::Dict merged = dict | other;
dict |= other;
py::Dict sum_dict = dict + other;
dict += other;

// Clear
dict.clear();
```

### `py::Function` — Callable Objects

```cpp
// From Python builtins
py::Function print = py::eval("print");
py::Function len = py::eval("len");
py::Function sorted = py::eval("sorted");

// Calling with positional arguments
print("Hello", "World", 42);

// Calling with keyword arguments
py::Dict kwargs = {{"sep", "-"}, {"end", "!\n"}};
print("A", "B", "C", kwargs);  // A-B-C!

// Using operator()
py::List list = {3, 1, 4, 1, 5};
py::List result = sorted(list);
long length = len(list);

// Custom Python function from string
py::Function add = py::eval("lambda x, y: x + y");
py::PyObj sum = add(10, 20);  // 30
```

## 🔧 Global Functions

### Printing

```cpp
py::List data = {1, 2, 3};

py::print(data);       // [1, 2, 3]
py::pprint(data);      // [
                       //     1,
                       //     2,
                       //     3
                       // ]
```

### Sequence Operations

```cpp
py::List list = {3, 1, 4, 1, 5};

long length = py::len(list);           // 5
py::Str type_name = py::type(list);    // "list"
py::List sorted_list = py::sorted(list);  // [1, 1, 3, 4, 5]
py::List reversed_list = py::reversed(list);  // [5, 1, 4, 1, 3]
```

### Boolean Operations

```cpp
py::List all_true = {1, 2, 3};
py::List some_false = {1, 0, 3};

bool a = py::all(all_true);    // true
bool b = py::all(some_false);  // false
bool c = py::any(some_false);  // true
```

### Map Function

```cpp
py::Function square = py::eval("lambda x: x ** 2");
py::List numbers = {1, 2, 3, 4, 5};
py::List squared = py::map(square, numbers);  // [1, 4, 9, 16, 25]
```

### Code Execution

```cpp
// Execute Python code
py::exec(R"(
    def greet(name):
        return f"Hello, {name}!"
)");

// Evaluate expression
py::PyObj result = py::eval("greet('World')");
py::print(result);  // Hello, World!

// Run Python file
py::run_file("script.py");

// Run file and get globals
py::PyObj globals = py::run_file_result("script.py");
py::Function my_func = globals["my_function"];
my_func();
```

## 📦 JSON Support

```cpp
// Python object to JSON string
py::Dict data = {
    {"name", "Alice"},
    {"age", 30},
    {"hobbies", py::List{"reading", "coding"}}
};

std::string json_str = py::json_dumps(data).str();
// {"name": "Alice", "age": 30, "hobbies": ["reading", "coding"]}

// Write to file
py::json_dump(data, "output.json", 4);  // 4 spaces indent

// Read from file
py::PyObj loaded = py::json_load("output.json");

// Parse JSON string
py::PyObj parsed = py::json_loads(json_str);
```

## 💡 Examples

### Example 1: Using Python's `random` Module

```cpp
#include "pyobj.h"
#include <iostream>

int main() {
    py::init_python();
    
    // Import random module
    py::exec("import random");
    py::Function random = py::eval("random.random");
    py::Function randint = py::eval("random.randint");
    py::Function choice = py::eval("random.choice");
    
    // Generate random numbers
    std::cout << "Random float: " << random() << std::endl;
    std::cout << "Random int 1-100: " << randint(1, 100) << std::endl;
    
    // Random choice from list
    py::List options = {"apple", "banana", "cherry"};
    std::cout << "Random fruit: " << choice(options) << std::endl;
    
    py::exit_python();
    return 0;
}
```

### Example 2: Working with Complex Python Objects

```cpp
py::init_python();

// Create complex nested structure
py::Dict person = {
    {"name", "Bob"},
    {"age", 25},
    {"address", py::Dict{
        {"street", "123 Main St"},
        {"city", "Boston"},
        {"zip", "02101"}
    }},
    {"hobbies", py::List{"gaming", "hiking", "photography"}}
};

// Access nested data
py::Str city = person["address"]["city"];
std::cout << "City: " << city << std::endl;

// Modify nested data
person["address"]["zip"] = "02102";
person["hobbies"].append("coding");

// Iterate over hobbies
py::List hobbies = person["hobbies"];
for (long i = 0; i < hobbies.len(); ++i) {
    std::cout << "Hobby " << i << ": " << hobbies[i] << std::endl;
}

// Pretty print entire structure
py::pprint(person);

py::exit_python();
```

### Example 3: Using Python's `requests` Library

```cpp
py::init_python();

// Import requests (must be installed)
try {
    py::exec("import requests");
    py::Function get = py::eval("requests.get");
    
    // Make HTTP request
    py::PyObj response = get("https://api.github.com/users/octocat");
    
    // Parse JSON response
    py::Dict data = response["json"]();
    
    std::cout << "User: " << data["login"] << std::endl;
    std::cout << "Name: " << data["name"] << std::endl;
    std::cout << "Repos: " << data["public_repos"] << std::endl;
    
} catch (...) {
    std::cerr << "Failed to import requests" << std::endl;
}

py::exit_python();
```

### Example 4: Formatted Strings with `py::fstring`

```cpp
// Named arguments
std::string msg1 = py::fstring(
    "User {name} is {age} years old.",
    py::farg("name", "Charlie"),
    py::farg("age", 28)
);
// "User Charlie is 28 years old."

// Positional arguments
std::string msg2 = py::fstring(
    "Coordinates: ({}, {}, {})",
    10, 20, 30
);
// "Coordinates: (10, 20, 30)"

// Mixed arguments
std::string msg3 = py::fstring(
    "{greeting} {name}! Your score is {}.",
    py::farg("greeting", "Hello"),
    py::farg("name", "David"),
    95
);
// "Hello David! Your score is 95."

// Escaped braces
std::string msg4 = py::fstring("Set: {{ {} }}", 42);
// "Set: { 42 }"
```

### Example 5: Custom Python Function from C++

```cpp
py::init_python();

// Define Python function
py::exec(R"(
    def fibonacci(n):
        if n <= 1:
            return n
        return fibonacci(n-1) + fibonacci(n-2)
)");

// Get function
py::Function fib = py::eval("fibonacci");

// Call from C++
for (int i = 0; i < 10; ++i) {
    py::PyObj result = fib(i);
    std::cout << "fib(" << i << ") = " << result << std::endl;
}

py::exit_python();
```

### Example 6: Using `py::Dict` as kwargs

```cpp
py::init_python();

// Function with kwargs
py::exec(R"(
    def describe_person(**kwargs):
        for key, value in kwargs.items():
            print(f"{key}: {value}")
)");

py::Function describe = py::eval("describe_person");

// Call with Dict as kwargs
py::Dict kwargs = {
    {"name", "Eve"},
    {"age", 22},
    {"city", "London"},
    {"occupation", "Engineer"}
};

describe(kwargs);
// Output:
// name: Eve
// age: 22
// city: London
// occupation: Engineer

py::exit_python();
```

## ⚠️ Requirements

| Requirement | Version | Notes |
|-------------|---------|-------|
| **C++ Compiler** | C++17+ | GCC 7+, Clang 5+, MSVC 2017+ |
| **Python Development Headers** | 3.x | `python3-dev` or `python3-devel` |
| **Python Library** | 3.x | `libpython3.x` |

### Installing Python Development Headers

```bash
# Debian/Ubuntu
sudo apt install python3-dev

# RHEL/CentOS/Fedora
sudo dnf install python3-devel

# Arch
sudo pacman -S python

# macOS (Homebrew)
brew install python@3
```

### Compilation Flags

```bash
# Get flags automatically
g++ -std=c++17 main.cpp -o main $(python3-config --includes --ldflags)

# Or manually
g++ -std=c++17 main.cpp -o main \
    -I/usr/include/python3.x \
    -lpython3.x
```

---

# Русский

## 📋 Обзор

**pyobj.h** — это **header-only библиотека C++17**, которая предоставляет **Python-подобный синтаксис** для встраивания Python в C++ приложения.

### Какую проблему решает?

Нативный Python C API **многословен и подвержен ошибкам**:

```cpp
// Традиционный Python C API — боль!
PyObject* list = PyList_New(0);
PyList_Append(list, PyLong_FromLong(42));
PyObject* item = PyList_GetItem(list, 0);
long value = PyLong_AsLong(item);
Py_DECREF(item);
Py_DECREF(list);
```

**С pyobj.h — чисто и интуитивно:**

```cpp
// pyobj.h — пишется как Python!
py::List list;
list.append(42);
long value = list[0];  // Автоматическая конвертация!
```

## ✨ Возможности

### Основные классы

| Класс | Python-эквивалент | Ключевые методы |
|-------|-------------------|-----------------|
| `py::PyObj` | `object` | Базовый класс, `operator[]`, `operator()`, `str()`, `is_*()` |
| `py::Str` | `str` | `upper()`, `lower()`, `split()`, `join()`, `find()`, `replace()` |
| `py::List` | `list` | `append()`, `pop()`, `extend()`, `sort()`, `reverse()`, `index()` |
| `py::Tuple` | `tuple` | `index()`, `count()`, `to_list()`, `operator[]` |
| `py::Set` | `set` | `add()`, `pop()`, `union_with()`, `intersection()`, `difference()` |
| `py::Dict` | `dict` | `get()`, `set()`, `pop()`, `keys()`, `values()`, `items()`, `update()` |
| `py::Function` | `callable` | `call()`, `operator()` с поддержкой kwargs |

### Глобальные функции

| Функция | Python-эквивалент | Описание |
|---------|-------------------|----------|
| `py::print(obj)` | `print()` | Печать любого Python-объекта |
| `py::pprint(obj)` | `pprint.pprint()` | Красивая печать с отступами |
| `py::len(obj)` | `len()` | Длина последовательности/коллекции |
| `py::type(obj)` | `type()` | Имя типа в виде строки |
| `py::sorted(seq)` | `sorted()` | Отсортированный список |
| `py::reversed(obj)` | `reversed()` | Обратная последовательность |
| `py::all(list)` | `all()` | True если все элементы истинны |
| `py::any(list)` | `any()` | True если хотя бы один элемент истинен |
| `py::map(func, list)` | `map()` | Применить функцию к каждому элементу |
| `py::exec(code)` | `exec()` | Выполнить строку кода Python |
| `py::eval(code)` | `eval()` | Вычислить выражение Python |
| `py::run_file(path)` | `exec(open().read())` | Выполнить файл Python |

## 🚀 Быстрый старт

### 📥 Установка

**Header-only библиотека — просто скопируй `pyobj.h` в проект!**

```bash
wget https://raw.githubusercontent.com/vk-candpython/pyobj/main/pyobj.h
```

### 🔨 Компиляция

```bash
g++ -std=c++17 main.cpp -o main $(python3-config --includes --ldflags)
```

### 💻 Минимальный пример

```cpp
#include "pyobj.h"

int main() {
    py::init_python();
    
    py::List my_list = {1, 2, 3, "hello"};
    my_list.append(42);
    
    py::print(my_list);  // [1, 2, 3, 'hello', 42]
    
    py::exit_python();
    return 0;
}
```

## 📚 Основные классы

*(См. английскую версию для подробного API каждого класса)*

## 💡 Примеры

### Пример 1: Использование модуля `random`

```cpp
py::init_python();

py::exec("import random");
py::Function randint = py::eval("random.randint");

std::cout << "Случайное число 1-100: " << randint(1, 100) << std::endl;

py::exit_python();
```

### Пример 2: Работа со сложными структурами

```cpp
py::init_python();

py::Dict person = {
    {"name", "Alice"},
    {"age", 30},
    {"hobbies", py::List{"reading", "coding"}}
};

py::Str name = person["name"];
person["age"] = 31;
person["hobbies"].append("gaming");

py::pprint(person);

py::exit_python();
```

## ⚠️ Требования

| Требование | Версия | Примечания |
|------------|--------|------------|
| **Компилятор C++** | C++17+ | GCC 7+, Clang 5+, MSVC 2017+ |
| **Python Development Headers** | 3.x | `python3-dev` или `python3-devel` |

```bash
# Установка в Ubuntu/Debian
sudo apt install python3-dev

# Компиляция
g++ -std=c++17 main.cpp -o main $(python3-config --includes --ldflags)
```

---

<div align="center">

**[⬆ Back to Top](#-pyobjh)**

*Python in C++ — As Easy as Python Itself*

</div>
