# Section Summary - Variables & Memory Management

!!! abstract "Summary"

    - Python garbage collector helps to avoid memory leaks by detecting circular references and destroy objects appropriately.
    - Never disable the garbage collector unless we have a good reason to do so.
    - In Python, variables don't associate with any particular types. Use the `type()` function to get the type of the objects that variables reference.
    - An object whose internal state cannot be changed is called immutable for example a number, a string, and a tuple. An object whose internal state can be changed is called mutable for example a list, a set, and a dictionary.
    - Use the `is` operator to check if two variables reference the same object. Use the `not` operator to negate the result of the `is` operator.
    - `None` is not equal to anything except itself.
