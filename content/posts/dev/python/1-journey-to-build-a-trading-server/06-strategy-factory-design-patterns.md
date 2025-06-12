+++
title = 'Design Patterns in Action: Strategy and Factory in a Trading App'
date  = 2024-11-07T09:30:00+03:00
tags  = ["python", "design patterns", "strategy pattern", "factory pattern", "trading app"]
draft = false
+++

Design patterns are reusable solutions to common problems in software development. In this post, I’ll explain how I used the **Strategy** and **Factory** patterns in my trading server to easily switch between broker APIs like Alpaca and BingX, and how adding new brokers is made simple.

---

### **The Problem**

Imagine you're building an app that interacts with multiple trading platforms (brokers). Each platform has its own API, but they all perform similar tasks: placing orders, fetching balances, and closing positions. We need a flexible way to switch between brokers without rewriting the core app logic.

This is where the **Strategy** and **Factory** patterns help.

---

### **What are the Strategy and Factory Patterns?**

### **Strategy Pattern**

The **Strategy Pattern** lets you define a family of algorithms, each one encapsulated, and makes them interchangeable. In our case, each broker (e.g., Alpaca, BingX) is a different strategy for trading operations.

### **Factory Pattern**

The **Factory Pattern** provides a way to create objects (like broker APIs) without specifying the exact class. It allows us to decide which broker to use based on runtime conditions, making it easy to add new brokers later.

---

### **How I Implemented These Patterns in the Trading App**

We’ll break this down into a few steps, starting with the broker interface.

---

### **1. The Broker Interface (Strategy)**

We define a generic broker interface that all broker classes must implement. It includes methods like `create_order()`, `get_balance()`, and `close_position()`.

```python
from abc import ABC, abstractmethod

class Broker(ABC):
    @abstractmethod
    def create_order(self, symbol: str, quantity: float, side: str):
        pass

    @abstractmethod
    def get_balance(self):
        pass

    @abstractmethod
    def close_position(self, symbol: str, quantity: float):
        pass
```

---

### **2. Concrete Broker Implementations**

We then implement this interface for Alpaca and BingX, each with its own logic for the operations.

**Alpaca API:**

```python
class AlpacaApi(Broker):
    def create_order(self, symbol: str, quantity: float, side: str):
        # Alpaca-specific code for creating an order
        pass

    def get_balance(self):
        # Alpaca-specific code for fetching balance
        pass

    def close_position(self, symbol: str, quantity: float):
        # Alpaca-specific code for closing a position
        pass
```

**BingX API:**

```python
class BingXApi(Broker):
    def create_order(self, symbol: str, quantity: float, side: str):
        # BingX-specific code for creating an order
        pass

    def get_balance(self):
        # BingX-specific code for fetching balance
        pass

    def close_position(self, symbol: str, quantity: float):
        # BingX-specific code for closing a position
        pass
```

---

### **3. The Factory (Factory Pattern)**

The Factory method decides which broker to use based on the broker's name. This allows the app to easily switch brokers at runtime.

```python
def get_broker(broker_name: str) -> Broker:
    if broker_name == "alpaca":
        return AlpacaApi()
    elif broker_name == "bingx":
        return BingXApi()
    else:
        raise ValueError(f"Unsupported broker: {broker_name}")
```

---

### **4. Usage Example**

Here’s how the app uses the broker. It decides which broker to use at runtime, making the code flexible and easy to extend with new brokers.

```python
def main():
    broker_name = "alpaca"  # This can change dynamically based on user input
    broker = get_broker(broker_name)

    # Now we can use the broker’s methods without worrying about which broker it is
    broker.create_order("AAPL", 10, "BUY")
    balance = broker.get_balance()
    print(balance)
    broker.close_position("AAPL", 5)
```

This setup means we can easily switch brokers by just changing the value of `broker_name`.

---

{{< adsense >}}

### **Conclusion**

The **Strategy Pattern** helped us isolate broker-specific logic, making it interchangeable. The **Factory Pattern** gives us flexibility in selecting which broker to use at runtime. By combining these patterns, our app becomes easy to maintain and extend, allowing us to add new brokers (like Interactive Brokers or others) without changing the core app logic.

These design patterns make the app scalable, flexible, and maintainable—ideal for adding new brokers and growing the trading platform.

{{< buymeacoffee-widget >}}