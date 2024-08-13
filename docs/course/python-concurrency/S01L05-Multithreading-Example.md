# Python Multithreading Example

We'll develop a multithreaded program that scraps the stock prices from the [Yahoo Finance website](https://finance.yahoo.com/).

## Extend the `Thread` class

We define a new class called `Stock` and place it in `stock.py` module as follow:

```py title="stock.py"
import threading
import requests
from lxml import html


class Stock(threading.Thread):
    def __init__(self, symbol: str) -> None:
        super().__init__()

        self.symbol = symbol
        self.url = f"https://finance.yahoo.com/quote/{symbol}"
        self.price = None

    def run(self):
        # Create a header for the request (1)
        headers = {
            "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36"
        }

        # Make a request to the URL
        response = requests.get(self.url, headers=headers)

        # The request is successful (2)
        if response.status_code == 200:
            # parse the HTML
            tree = html.fromstring(response.text)
            # get the price in text (3)
            price_text = tree.xpath(
                '//*[@id="nimbus-app"]/section/section/section/article/section[1]/div[2]/div[1]/section/div/section[1]/div[1]/fin-streamer[1]/span/text()'
            )
            # Remove the comma and convert it to a number
            if price_text:
                try:
                    self.price = float(price_text[0].replace(",", ""))
                except ValueError:
                    self.price = None

    def __str__(self):
        return f"{self.symbol}\t{self.price}"
```

1. Notice that without valid headers, Yahoo will return 404 instead of 200.
2. In this case, we get the HTML contents from the response and pass it to the `fromstring()` function of the `html` module from the `xml` package.
3. Every element on a webpage can be selected using something called XPath.

    To get the XPath of an element using Google Chrome, you inspect the page, right-click the element, select Copy, and Copy XPath.

    The XPath of the stock price at the time of writing this code is as follows. Notice that if Yahoo changes the page structure, we need to change the XPath accordingly. Otherwise, the program won't work as expected.

    Finally, to get the text of the element, we append the `text()` at the end of the XPath.

## Using the `Stock` class

The following `main.py` module uses `Stock` class from the `stock.py` module:

```py title="main.py"
from stock import Stock

symbols = ['MSFT', 'GOOGL', 'AAPL', 'META']
threads = []

# Create a thread for each symbol, start it, and append the thread to the thread list
for symbol in symbols:
    t = Stock(symbol)
    threads.append(t)
    t.start()

# Wait for all the threads in the threads list to complete and print out the stock price
for t in threads:
    t.join()
    print(t)

```

```title="Output"
MSFT    402.69
GOOGL   162.03
AAPL    213.31
META    509.63
```

!!! abstract "Summary"
    Define a class that inherits from the `threading.Thread` class and override the `run()` method.