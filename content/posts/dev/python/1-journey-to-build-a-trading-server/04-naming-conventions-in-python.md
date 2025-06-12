+++
title = 'Understanding Naming Conventions in Python'
date  = 2024-11-07T07:07:44+03:00
tags  = ["python", "naming conventions", "pep 8", "best practices"]
draft = false
+++

If you're an iOS developer like me, you're probably used to CamelCase conventions from Objective-C and Swift, where even functions and variables follow this style. Switching to Python, you’ll find that snake_case is preferred for many elements, such as functions and variables, while CamelCase remains for class names. Adjusting to these conventions can feel a bit different, but embracing Python's style will make your code easier for others to read and more aligned with community standards.

In this post, we’ll explore Python’s main naming conventions, following the popular PEP 8 style guide.

## 1. Class and Type Names

Python class and type names should use **CamelCase** (or PascalCase), where each word starts with an uppercase letter and there are no underscores. This style makes these names stand out, helping to distinguish them from functions or variables. Types, which are often used to represent specific data structures (like `UserProfile` or `OrderDetails`), follow the same CamelCase convention.

**Examples:**

```python
class AlpacaApi:
    pass

class BingXApi:
    pass

class UserProfile:
    pass

class OrderDetails:
    pass
```

**Exceptions:** Exception classes also use CamelCase, helping them stand out from other classes.

**Example:**

```python
class APIError(Exception):
    pass

class FileNotFoundError(Exception):
    pass

class InvalidInputError(Exception):
    pass
```

## 2. Function and Method Names

Functions and methods in Python use **snake_case**, meaning lowercase letters with underscores between words. This convention fits Python’s focus on simplicity and readability.

**Examples:**

```python
def handle_order(order: Order):
    pass

def is_valid_exchange(exchange: str) -> bool:
    pass
```

For methods within a class, the same snake_case style applies, whether the method is public or private (indicated by a leading underscore).

**Example:**

```python
class AlpacaApi:
    def fetch_account_details(self):
        pass

		def _map_order_side(self, side: str) -> OrderSide:
        pass
```

## 3. Variable Names

Like functions, variable names in Python also use **snake_case** for both global and local variables.

**Examples:**

```python
api_key = os.environ.get('API_KEY')

class AlpacaApi:
	def create_order(self, symbol: str, side: str, qty: float):
	    order_side = self._map_order_side(side)
	    ...
```

**Constants:** For constants (variables that shouldn’t change), use **UPPERCASE** with underscores between words. Constants are often defined at the top of a module.

**Example:**

```python
SUPPORTED_EXCHANGES = [ "ALPACA", "BINANCE", "BINGX" ]
```

## 4. Module and Package Names

File and directory names in Python, which represent modules and packages, should use **lowercase letters** with underscores if needed. This applies to both single- and multi-word names.

**Examples:**

- Single-word module: `config.py`
- Multi-word module: `api_error.py`
- Package name: `my_package`

This naming style makes it easy to identify modules and packages within a project.

## 5. Adapting as an iOS Developer

If you’re used to Swift or Objective-C, where CamelCase is common for everything, Python’s use of snake_case for functions, methods, and variables may feel unfamiliar at first. But adapting to Python’s conventions helps make your code more idiomatic and easier for others to read. While it’s natural to stick with CamelCase for class and type names, embracing snake_case for other identifiers aligns with Python’s clean and readable style.

## 6. Why Naming Conventions Matter

Consistent naming conventions improve your code’s readability and make it more intuitive for other developers to understand. Here’s why it matters:

- **Improved Readability**: Consistent names make it clear what each part of the code does.
- **Better Collaboration**: Naming conventions make it easier for team members to navigate the code.
- **Reduced Errors**: Clear names reduce the chance of mistakes, especially in maintenance.

{{< adsense >}}

## Conclusion

Python’s naming conventions are essential for clean, readable, and maintainable code. By following conventions for classes, functions, variables, modules, and constants, you ensure your code fits Python’s philosophy of simplicity and readability.

As you work with Python, keep these conventions in mind. They’re not just rules—they’re tools to help you write better code. Whether it’s a small script or a large app, consistent naming makes Python code more professional and manageable.

{{< buymeacoffee-widget >}}