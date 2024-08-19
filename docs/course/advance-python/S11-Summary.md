# Generators

## Python Generators

To pause a function midway and resume from where the function was paused, we use the `yield` statement.

A **generator** is a function that contains at least one `yield` statement.

When we call a generator function, it returns a new generator object. However, it doesn't start the function.

Generator objects (or generators) implement the iterator protocol.

!!! example

    ```py
    def squares(length):
        for n in range(length):
            yield n ** 2

    length = 5
    square = squares(length)
    for s in square:
        print(s)
    ```

    ```title="Output"
    0
    1
    4
    9
    16
    ```

## Generator Expressions

!!! example

    ```py
    square_generator = (n**2 for n in range(5))

    print(next(square_generator))
    print(next(square_generator))
    print(next(square_generator))
    print(next(square_generator))
    print(next(square_generator))
    print(next(square_generator))
    ```

    ```title="Output"
    0
    1
    4
    9
    16
    Traceback (most recent call last):
    File "/Users/duynm/duynm/me/studying/AdvancePython/Section 11. Generators/test.py", line 8, in <module>
        print(next(square_generator))
    StopIteration
    ```

!!! abstract "Summary"

    -   Python **generators** are functions that contain at least one `yield` statement.
    -   A generator function returns a generator object.
    -   A generator object is an iterator. Therefore, it becomes exhausted once there's no remaining item to return.
    -   Use a Python generator expression to return a generator.
