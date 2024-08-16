# Python Static Methods

## Introduction to Python static methods

Static methods aren't bound to an object. In other words, static methods cannot access and modify an object state.

In addition, Python doesn't implicitly pass the `cls` parameter (or the `self` parameter) to static method. Therefore, static methods cannot access and modify the class's state.

In practice, we **use static methods to define utility methods or group functions that have some logical relationships in a class**.

To define a static method, we use the `@staticmethod` decorator:

!!! example

    ```py
    class TemperatureConverter:
        KEVIN = "K"
        FAHRENHEIT = "F"
        CELSIUS = "C"

        @staticmethod
        def celsius_to_fahrenheit(c):
            return 9 * c / 5 + 32

        @staticmethod
        def fahrenheit_to_celsius(f):
            return 5 * (f - 32) / 9

        @staticmethod
        def celsius_to_kelvin(c):
            return c + 273.15

        @staticmethod
        def kelvin_to_celsius(k):
            return k - 273.15

        @staticmethod
        def fahrenheit_to_kelvin(f):
            return 5 * (f + 459.67) / 9

        @staticmethod
        def kelvin_to_fahrenheit(k):
            return 9 * k / 5 - 459.67

        @staticmethod
        def format(value, unit):
            symbol = ""
            if unit == TemperatureConverter.FAHRENHEIT:
                symbol = "°F"
            elif unit == TemperatureConverter.CELSIUS:
                symbol = "°C"
            elif unit == TemperatureConverter.KEVIN:
                symbol = "°K"

            return f"{value}{symbol}"


    if __name__ == "__main__":
        f = TemperatureConverter.celsius_to_fahrenheit(35)
        print(TemperatureConverter.format(f, TemperatureConverter.FAHRENHEIT))
    ```

## Python static methods vs class methods

| Class Methods                                               | Static Methods                                                      |
|-------------------------------------------------------------|---------------------------------------------------------------------|
| Python implicitly pass the `cls` argument to class methods. | Python doesn’t implicitly pass the `cls` argument to static methods |
| Class methods can access and modify the class state.        | Static methods cannot access or modify the class state.             |
| Use `@classmethod` decorators to define class methods       | Use `@staticmethod` decorators to define static methods.            |

!!! abstract "Summary"

    - Use static methods to define utility methods or group a logically related functions into a class.
    - Use the `@staticmethod` decorator to define a static method.