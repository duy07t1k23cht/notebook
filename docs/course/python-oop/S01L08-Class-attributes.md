# Python Class Attributes

## Introduction to class attributes

The class attributes don’t associate with any specific instance of the class. But they’re shared by all instances of the class.

!!! note
    Class attributes are similar to the static members in Java or C#, but not the same.

To define a class attribute, we place it outside of the `__init__()` method.

After that, we can access the class attribute via instances of the class or via the class name.

!!! example
    The following defines `pi` as a class attribute:
    ```py
    class Circle:
        pi = 3.14159  #(1)

        def __init__(self, radius):
            self.radius = radius

        def area(self):
            return self.pi * self.radius**2  #(2)

        def circumference(self):
            return 2 * self.pi * self.radius #(3)

    c = Circle(10)
    print(c.pi)       #(4)
    print(Circle.pi)  #(5)
    ```

    1. Defines `pi` as a class attribute by placing it outside of the `__init__()` method.
    2. Access the `pi` class attribute via the `self` variable.
    3. Access the `pi` class attribute via the `self` variable.
    4. Access the `pi` class attribute via an instance of the `Circle` class.
    5. Access the `pi` class attribute directly via the `Circle` class.

    ```title="Output"
    3.14159
    3.14159
    ```

## How Python class attributes work

!!! example
    ```py
    class Test:
        x = 10

        def __init__(self):
            self.x = 20


    test = Test()
    print(test.x)  # 20
    print(Test.x)  # 10
    ```
    ```title="Output"
    20
    10
    ```

## When to use Python class attributes

- Storing class constants
- Tracking data accross of all instances
- Defining default values

!!! abstract "Summary"
    - A class attribute is shared by all instances of the class. To define a class attribute, we place it outside of the `__init__()` method.
    - Use class attributes for storing class contants, track data across all instances, and setting default values for all instances of the class.