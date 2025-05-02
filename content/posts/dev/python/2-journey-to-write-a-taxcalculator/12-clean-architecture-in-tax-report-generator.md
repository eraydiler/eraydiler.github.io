+++
title = "Clean Architecture in a Financial Report Generator"
date = 2025-04-29T14:05:00+03:00
tags = ["introduction", "python", "flask", "personal project", "tax calculator", "financial report", "parser", "csv", "clean architecture"]
draft = false
+++

Have you ever worked on a project that became a nightmare to maintain? Where changing one small thing broke something completely unrelated? That’s the case where the Clean Architecture comes into play, and I'll explain how I used it lately on an application that processes financial data from Interactive Brokers.

## What is Clean Architecture?

Think of Clean Architecture like an onion with layers. The inner layers contain your most important business rules, while outer layers handle technical details. The key rule: inner layers don't depend on outer layers.

{{< figure src="../clean-architecture-diagram.webp" alt="Clean Architecture Diagram" class="center" >}}

## My Parsing Project

The application needs to:

- Parse CSV files from Interactive Brokers
- Filter different transaction types (trades, fees, dividends)
- Calculate tax declarations
- Generate a new CSV report

## Clean Architecture in Action

Let's break down the implementation into four simple layers:

### 1. Domain Layer (The Core)

This layer contains our basic business entities and rules. For example, a trade:

```python
class Trade:
    def __init__(self, symbol, quantity, price):
        self.symbol = symbol
        self.quantity = quantity
        self.price = price

    def calculate_value(self):
        return self.quantity * self.price
```

Notice this class knows nothing about CSVs, files, parsing, or how data is stored. It simply represents what a trade is and the relevant logic.

### 2. Use Cases (What The App Does)

This layer contains the actual things our application can do:

```python
class ReportService:
    def __init__(self, parsers, writer):
        self.parsers = parsers
        self.writer = writer

    def process_report(self, input_file):
        data = self._parse_input(input_file)
        processed_data = self._apply_business_rules(data)
        return self.writer.write(processed_data)
```

The service orchestrates actions but doesn't know HOW parsing or writing happens - it just knows it needs these things.

### 3. Interface Adapters (The Contracts)

These are like promises that define what capabilities we need:

```python
from typing import Protocol, List

class ReportWriter(Protocol):
    def write(self, data: List[dict]) -> bool:
        """Write processed data to a report"""
        pass

class ParserProtocol(Protocol):
    def parse(self, file_content: str) -> List[dict]:
        """Parse external data into domain objects"""
        pass
```

When using protocols in Python, we can explicitly implement them by inheriting from the protocol class:

### 4. External Details (Technical Implementation)

Finally, we have concrete implementations:

```python
class CSVReportWriter(ReportWriter):
    def write(self, data: List[dict]) -> bool:
        try:
            with open(self.filepath, 'w', newline='') as f:
                writer = csv.DictWriter(f, fieldnames=self.headers)
                writer.writeheader()
                writer.writerows(data)
            return True
        except Exception:
            return False

class CSVParser(ParserProtocol):
    def parse(self, csv_content: str) -> List[Trade]:
        """Convert CSV data into domain objects"""
        trades = []
        # Parsing logic to convert CSV rows into Trade objects
        return trades
```

This is where we actually deal with files, CSV formatting, etc.

## The Magic: Pluggability

Here's where Clean Architecture shines. Want to change how reports are written? Just create a new class that follows the ReportWriter contract:

```python
class ExcelReportWriter(ReportWriter):
    def write(self, data: List[dict]) -> bool:
        # Excel-specific code here
        return True
```

And plug it in! No changes needed to domain logic or use cases.

## Benefits in Practice

1. **Testing is easier**: You can test business rules without needing actual CSV files
2. **Changes stay contained**: Format changes in IBKR's CSV only affect the parser, not tax calculations
3. **Flexibility**: Need PDF output instead of CSV? Just add a new writer implementation

## Real-World Application

In my project, this architecture made it possible to:

- Add support for different brokers by just creating new parsers
- Change tax calculation rules without touching file handling code
- Create both command-line and web interfaces using the same core code

Clean Architecture might seem like extra work at first, but when requirements change (and they always do), you'll be grateful for the organization and flexibility it provides.

Have you tried Clean Architecture in your projects? What challenges did you face?

## Conclusion

Here’s a small but robust and scalable app that is open to adding new parsers, brokers, and supporting completely different formats, etc. I hope I’ve shed some light on the topic.