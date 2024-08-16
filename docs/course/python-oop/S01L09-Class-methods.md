# Python Class Methods

## Introduction to Python class methods

A class method isn’t bound to any specific instance. It’s bound to the class only.

To define a class method:

- Place the `@classmethod` decorator above the method definition.
- Remane the `self` parameter to `cls`.

A class method cannot access instance attributes. But it can access class attributes via the `cls` variable.

!!! example
    
    ```py
    class Person:
        def __init__(self, first_name, last_name, age):
            self.first_name = first_name
            self.last_name = last_name
            self.age = age

        def get_full_name(self):
            return f"{self.first_name} {self.last_name}"

        def introduce(self):
            return f"Hi. I'm {self.first_name} {self.last_name}. I'm {self.age} years old."

        @classmethod
        def create_anonymous(cls):
            return Person('John', 'Doe', 25)
    ```

    The `create_anonymous()` method cannot access instance attributes. But it can access class attributes via the `cls` variable.

## Calling Python class methods

!!! example

    ```py
    anonymous = Person.create_anonymous()
    print(anonymous.introduce())
    ```

## Class methods vs. instance methods

| Features  | Class methods     | Instance methods            |
|-----------|-------------------|-----------------------------|
| Binding   | Class             | An instance of the class    |
| Calling   | `Class.method()`  | `object.method()`           |
| Accessing | Class attributes  | Instance & class attributes |

## When to use Python class methods

In practice, we often use class methods for methods that create an instance of the class.

We can use class methods for any methods that are not bound to a specific instance but the class.

!!! note

    When a method creates an instance of the class and returns it, the method is called a factory method.

!!! abstract "Summary"

    - Python class methods aren't bound to any specific instance, but classes.
    - Use `@classmethod` decorator to change an instance method to a class method. Also, pass the `cls` as the first parameter to the class method. Access class attributes via the `cls` variable.
    - Use class methods for factory methods.