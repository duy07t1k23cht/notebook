# Python Decorators

## What is a decorator in Python?

A **decorator** is a function that takes another function as an argument and extends its behavior without changing the original function explicitly.

In general, a decorator is:

-   A function that takes another function (original function) as an argument and returns another function (or closure)
-   The closure typically accepts any combination of positional and keyword-only arguments
-   The closure function calls the original function using the arguments passed to the closure and returns the result of the function.

## Decorator example

!!! example

    ```py
    from functools import wraps

    def currency(fn):  #(1)
        @wraps(fn)
        def wrapper(*args, **kwargs):  #(2)
            result = fn(*args, **kwargs)  #(3)
            return f'${result}'
        return wrapper  #(4)

    @currency
    def net_price(price, tax):
        """ calculate the net price from price and tax
        Arguments:
            price: the selling price
            tax: value added tax or sale tax
        Return
            the net price
        """
        return price * (1 + tax)

    help(net_price)
    print(net_price.__name__)
    ```

    1. The `currency` decorator is a function that takes another function as an argument.
    2. We define a closure that accepts any combination of positional and keyword-only arguments.
    3. The closure function calls the original function using the arguments passed to the closure and returns the result of the function.
    4. The decorator return the closure.

    ```title="Output"
    net_price(price, tax)
        calculate the net price from price and tax
        Arguments:
            price: the selling price
            tax: value added tax or sale tax
        Return
            the net price

    net_price
    ```

## Decorator with Arguments

!!! example

    ```py
    from functools import wraps

    def repeat(times):
        ''' call a function a number of times '''
        def decorate(fn):
            @wraps(fn)
            def wrapper(*args, **kwargs):
                for _ in range(times):
                    result = fn(*args, **kwargs)
                return result
            return wrapper
        return decorate

    @repeat(10)
    def say(message):
        ''' print the message
        Arguments
            message: the message to show
        '''
        print(message)

    say('Hello')
    ```
    ```title="Output"
    Hello
    Hello
    Hello
    Hello
    Hello
    Hello
    Hello
    Hello
    Hello
    Hello
    ```

## Class decorator

!!! example

    ```py
    from functools import wraps

    class Star:
        def __init__(self, n):
            self.n = n

        def __call__(self, fn):
            @wraps(fn)
            def wrapper(*args, **kwargs):
                print(self.n*'*')
                result = fn(*args, **kwargs)
                print(result)
                print(self.n*'*')
                return result
            return wrapper

    @Star(5)
    def add(a, b):
        return a + b

    add(10, 20)
    ```

!!! abstract "Summary"

    - A decorator is a function that changes the behavior of another function without explicitly modifying it.
    - Use `wraps` function from the `functools` built-in module to retain the documentation and name of the original function.
    - Use a factory decorator to return a decorator that accepts arguments.
    - Use callable classes as decorators by implementing the `__call__` method. Pass the decorator arguments to the `__init__` method.
