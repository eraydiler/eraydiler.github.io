+++
title = 'Mocking and Unit Testing in Python: A Trading App Example'
date  = 2024-11-11T08:00:00+03:00
tags  = ["python", "unit testing", "mocking", "trading app", "unittest"]
draft = false
+++

I’ve always been an advocate for writing tests. After all, who would want to drive a car that hasn’t undergone testing? In the software industry, especially in startups, testing often takes a backseat. The main reason is typically a lack of time. Writing tests is a task on its own, and limited time is usually prioritized for development instead.

This article isn’t about the necessity of testing, but as someone who has worked on dozens of projects without proper tests, I’ve often wondered: Wouldn’t everything have been easier if tests had been in place? Most recently, I added unit tests to a trading server application I’ve been developing. In this article, I’ll share examples of the tests I implemented, the issues I encountered, and the solutions I found.

## What Is the Purpose of Unit Tests?

The goal of unit tests is to verify whether specific methods in your project work as expected. By writing unit tests, you can focus solely on testing the implementation of the methods themselves, independent of external factors like network requests.

These tests allow you to run them regularly during development to ensure that everything is functioning correctly. Beyond maintaining the overall health of your project, tests can also serve as documentation. They can help you address forgotten or unclear scenarios, enabling you to continue development with greater confidence.

## Real-life example

To make things easier to understand, consider the following project structure:

```swift
project_name/
│
├── src/
│   ├── api/
│   │   ├── __init__.py
│   │   ├── alpaca_api.py
│   │   └── ...
│   └── ...
│
├── tests/
│   ├── __init__.py
│   ├── alpaca_api_test.py
│   └── ...
│
...
```

## Why Mocking?

The purpose of unit tests is to test the code of your project. For example, tests written for the `AlpacaApi` class should only verify the implementation of that class, independent of network requests.

## Mocking with `unittest.mock`

In my project, I created a separate test class for each broker API I added. One of these is the `TestAlpacaApi` class, which tests the `AlpacaApi` class. Below, you can find two methods from this test class: one tests the success case for order placement, and the other tests the failure case.

The `AlpacaApi` class uses a `trading_client` object from the `alpaca-py` library to send network requests. Therefore, I mocked the `trading_client` in my test code.

In Python’s `unittest` framework, the `patch` function from the `unittest.mock` module is invaluable for mocking. Here’s how I used it to replace the real network request with a mock that returns a predefined response:

## Sending order tests in `alpaca_api_test.py`

```python
import unittest
from api.api_error import APIError
from api.alpaca_api import AlpacaApi

class TestAlpacaApi(unittest.TestCase):

    @patch('api.alpaca_api.TradingClient')
    def setUp(self, mock_trading_client):
        # Create a mock instance of the TradingClient
        self.mock_trading_client = mock_trading_client.return_value
        
        # Instantiate the AlpacaApi class for testing
        self.api = AlpacaApi()

    def test_create_order_success(self):
        # Mock order response
        mock_order = MagicMock()
        mock_order.status = 'filled'
        self.mock_trading_client.submit_order.return_value = mock_order

        # Call the method
        api_success = self.api.create_order('AAPL', 'BUY', 10)

        # Assertions
        self.mock_trading_client.submit_order.assert_called_once()
        self.assertEqual(api_success.details["status"], 'filled')

    def test_create_order_failure(self):
        # Simulate an order failure
        self.mock_trading_client.submit_order.side_effect = Exception("API error")

        # Test that APIError is raised
        with self.assertRaises(APIError):
            self.api.create_order('AAPL', 'BUY', 10)
```

## Implementation in `alpaca_api.py`

Here’s how the `create_order` method is implemented in the `AlpacaApi` class:

```python
class AlpacaApi:
  """
  Alpaca API integration for trading actions like fetching account details, 
  placing orders, and managing positions.
  """

	...

	def create_order(self, symbol: str, side: str, qty: float):
	    """
	    Creates a market order for the given symbol.
	    
	    :param symbol: The stock ticker symbol (e.g., 'AAPL').
	    :param side: The order side ('BUY' or 'SELL').
	    :param qty: The quantity to trade.
	    :raises ValueError: If the side is not 'BUY' or 'SELL'.
	    """
	    order_side = self._map_order_side(side)
	
	    # Preparing the market order request
	    try:
	        data = MarketOrderRequest(
	            symbol=symbol,
	            qty=qty,
	            side=order_side,
	            time_in_force=TimeInForce.DAY
	        )
	
	        # Submitting the market order
	        order = self.trading_client.submit_order(order_data=data)
	        return APISuccess(f"Order for {qty} {symbol} shares placed successfully.", details={"status": order.status})
	    except Exception as e:
	        raise APIError(f"Failed to create order: {str(e)}", details={"method": "create_order", "symbol": symbol})
```

As you can see, the request is sent via the `self.trading_client.submit_order(order_data=data)` line. In the test code, this exact line is mocked.

## Conclusion

I firmly believe that writing tests is crucial for software development, especially from a developer’s perspective. Tests not only serve as documentation but also empower developers to make changes confidently without fear of breaking things. By adding tests to my first real-life Python project, I gained valuable insights into the process and took proactive steps to ensure the project's health and stability.

I encourage you to take the time to implement testing in your projects. It will pay off in the long run by helping you catch issues early and maintain a high-quality codebase.

{{< buymeacoffee-widget >}}