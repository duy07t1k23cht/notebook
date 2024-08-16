# Python Private Attributes

## Private attributes

**Python doesn't have a concept of private attributes**. In other words, all attributes are accessible from the outside of a class.

By convention, we can define a private attribute by prefixing a single underscore (`_`).

!!! example
    ```py
    class Counter:
        def __init__(self):
            self._current = 0

        def increment(self):
            self._current += 1

        def value(self):
            return self._current

        def reset(self):
            self._current = 0
    ```

## Name mangling with double underscores

If we prefix an attribute name with double underscores (`__`) like this:
```py
__attribute
```
Python will automatically change the name of the `__attribute` to:
```py
_class__attribute
```
This is called the name mangling in Python.

By doing this, we cannot access the `__attribute` directly from the outside of a class like:
```py
instance.__attribute
```
However, we still can access it using the `_class__attribute` name:
```py
instance._class__attribute
```

!!! example
    The following example redegfines the Counter class with the `__current` attribute:
    ```py
    class Counter:
        def __init__(self):
            self.__current = 0

        def increment(self):
            self.__current += 1

        def value(self):
            return self.__current

        def reset(self):
            self.__current = 0
    ```
    Now, if we attempt to access `__current` attribute, we’ll get an error:
    ```py
    counter = Counter()
    print(counter.__current)
    ```
    ```title="Output"
    AttributeError: 'Counter' object has no attribute '__current'
    ```
    However, we can access the attribute as `_Counter___current`:
    ```py
    counter = Counter()
    print(counter._Counter__current)
    ```

!!! abstract "Summary"
    - **Python doesn't have a concept of private attributes**. In other words, all attributes are accessible from the outside of a class.
    - Prefix an attribute with a single underscore (`_`) to make it private by convention.
    - Prefix an attribute with double underscores (`__`) to use the name mangling.