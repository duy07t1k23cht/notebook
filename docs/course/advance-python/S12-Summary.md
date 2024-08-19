# Context Manager

## Python `with` statement

Here is the typical syntax of the `with` statement:

```py
with context as ctx:
    # use the the object

# context is cleaned up
```

How it works:

- When Python encouters the `with` st atement, it creates a new context. The context can optionally return an `object`.
- After the `with` block, Python cleans up the context automatically
- The scope of the `ctx` has the same scope as the `with` statement. It means that you can access the `ctx` both inside and after the `with` statement.

## Python context manager protocol

Python context managers work based on the context manager prototol.

The context manager protocol has the following methods:

- `__enter__()` - setup the context and optionally returns some object
- `__exit__()` - cleanup the object

<figure markdown="span">
    ![](https://www.pythontutorial.net/wp-content/uploads/2020/11/Python-Context-Manager.png)
</figure>

!!! example

    ```py
    class File:
        def __init__(self, filename, mode):
            self.filename = filename
            self.mode = mode

        def __enter__(self):
            print(f'Opening the file {self.filename}.')
            self.__file = open(self.filename, self.mode)
            return self.__file

        def __exit__(self, exc_type, exc_value, exc_traceback):
            print(f'Closing the file {self.filename}.')
            if not self.__file.closed:
                self.__file.close()

            return False

    with File('data.txt', 'r') as f:  #(1)
        print(int(next(f)))
    ```

    1.  !!! warning "The `f` references the object returned by the `__enter__()` method. It doesn't reference the instance of the `File` class."

!!! abstract "Summary"

    - Use Python context manager to define runtime contexts when executing in the `with` statement.
    - Implement the `__enter__()` and `__exit__()` methods to support the context manager protocol.