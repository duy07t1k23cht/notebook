# Named Tuple

A **named tuple** is a subclass of `tuple`. On top of that, it adds property names to the positional elements.

!!! example

    ```py
    from collections import namedtuple

    Point = namedtuple("Point2D", ["x", "y"])

    A = Point(6, 9)
    B = Point(x=5, y=3)

    print(A._fields)
    print(A.x, A.y)
    print(B.x, B.y)
    ```

    ```title="Output"
    ('x', 'y')
    6 9
    5 3
    ```

!!! abstract "Summary"

    - Named tuples are tuples whose element positions have meaningful names.
    - Use the `namedtuple` function from the `collections` standard library to create a named tuple class.
    - Named tuples are immutable.
