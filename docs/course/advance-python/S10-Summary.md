# Iterators and Iterables

## Iterator

An iterator is an object that implements the **iterator protocol**:

-   `__iter__` method that returns the object itself
-   `__next__` method that returns the next item. If all the items have been returned, the method raises a `StopIteration` exception.

!!! example

    ```py
    class Square:
        def __init__(self, n) -> None:
            self.n = n
            self.curr = 0

        def __iter__(self):
            return self

        def __next__(self):
            if self.curr >= self.n:
                raise StopIteration

            self.curr += 1
            return self.curr**2


    square = Square(5)
    for i in square:
        print(i)

    next(square)
    ```

    ```title="Output"
    1
    4
    9
    16
    25
    Traceback (most recent call last):
    File "/Users/duynm/duynm/me/studying/AdvancePython/Section 10. Iterators and Iterables/test.py", line 21, in <module>
        next(square)
    File "/Users/duynm/duynm/me/studying/AdvancePython/Section 10. Iterators and Iterables/test.py", line 11, in __next__
        raise StopIteration
    StopIteration
    ```

## Iterables

An object is iterable when it implements the `__iter__` method and its `__iter__` method returns a new iterator.

!!! example

    ```py
    class Colors:
        def __init__(self):
            self.rgb = ['red', 'green', 'blue']

        def __len__(self):
            return len(self.rgb)

        def __iter__(self):
            return self.ColorIterator(self)

        class ColorIterator:
            def __init__(self, colors):
                self.__colors = colors
                self.__index = 0

            def __iter__(self):
                return self

            def __next__(self):
                if self.__index >= len(self.__colors):
                    raise StopIteration

                # return the next color
                color = self.__colors.rgb[self.__index]
                self.__index += 1
                return color
    ```

## Python `iter`

The `iter()` function required an argument that can be an iterable or a sequence. In general, the object argument can be any object that supports either iteration or sequence protocol.

The following flowchart illustrates how the `iter()` function works:

<figure markdown="span">
    ![](https://www.pythontutorial.net/wp-content/uploads/2020/11/Python-iter.png)
</figure>

!!! abstract "Summary"

    - An iterable is an object that implements the `__iter__` method which returns an iterator.
    - An iterator is an object that implements the `__iter__` method which returns itself and the `__next__` method which returns the next element.
    - Iterators are also iterables
    - Use the Python `iter()` function to get in iterator of an object.