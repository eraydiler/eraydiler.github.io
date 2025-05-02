+++
title = "Journey to Write a TaxCalculator"
date = 2025-04-29T13:49:00+03:00
tags = ["introduction", "python", "flask", "personal project", "tax calculator", "financial report", "parser", "csv", "unit test", "clean architecture"]
draft = true
+++

As the world underwent changes during the pandemic, my life also experienced significant transformations. One of these changes was my journey into learning about trading and investments. Since then, I've been actively incorporating everything I've learned into my daily life, and I can confidently say I've made substantial progress. For instance, while I initially knew nothing about Funds, today I actively manage my portfolio through medium-term swing trading and execute vertical credit spreads option day trading using my own written strategy.

Since these trading activities are subject to taxation, I need to calculate and declare the resulting taxes every March. Particularly, the number of daily trades I execute with options is quite significant. Calculating all these transactions manually would be both laborious and prone to errors. Therefore, I decided to develop an application that would take all my annual trading activities from my broker IBKR's report and generate tax declarations compliant with Turkish regulations. Thanks to this application, I've solved the complex tax declaration process for myself.

I developed this application using VSCode and GitHub Copilot. I've been extensively using AI since its inception and find it incredibly beneficial. With this project, I completed my first end-to-end development with Copilot. Throughout the process, Copilot and I worked like two colleagues, helping each other when one got stuck, ultimately resulting in a successful project. As a result, I was able to easily calculate my tax declaration for this year.

Specifically for this project, I organized the development phases into three main categories based on the challenges I encountered: architecture selection, technical challenges, and unit tests that I added to facilitate potential future changes.

Related articles:

## Architecture Decisions

---

### [Clean Architecture in Python: Lessons from a Financial Report Generator](link-to-article)

### Key Takeaways

- Separating business logic from I/O operations
- Using protocols for dependency inversion
- Managing complex data transformations
- Testing strategies for financial calculations

## Technical Challenges

---

### [Handling CSV Data in Python: Beyond pandas.read_csv()](link-to-article)

### Common Challenges

- Dealing with multi-section CSV files
- Validating financial data
- Memory efficiency with large datasets
- Error handling strategies

## Testing Approaches

---

### [Integration Testing Financial Applications](link-to-article)

### Testing Patterns

- Separating test creation and validation
- Managing test data without exposing sensitive information
- Comparing complex financial calculations
- Handling floating-point comparisons