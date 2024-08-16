# Section Summary - Special methods

## `__str__`

Implement the `__str__` method to customize the string representation of an instance of a class.

!!! example

    ```py
    class Person:
        def __init__(self, first_name, last_name, age):
            self.first_name = first_name
            self.last_name = last_name
            self.age = age

        def __str__(self):
            return f'Person({self.first_name}, {self.last_name}, {self.age})'

    person = Person('John', 'Doe', 25)
    print(person)
    ```
    ```py title="Output"
    Person(John, Doe, 25)
    ```

## `__repr__`

The `__repr__` method defines behavior when we pass in instance of a class to the `repr()`.

The `__repr__` method returns the string representation of an object.

Typically, the `__repr__()` returns a string that can be executed and yield the same value as the object.

!!! example

    ```py
    class Person:
        def __init__(self, first_name, last_name, age):
            self.first_name = first_name
            self.last_name = last_name
            self.age = age

        def __repr__(self):
            return f'Person("{self.first_name}","{self.last_name}",{self.age})'
    
    person = Person("John", "Doe", 25)
    print(repr(person))
    ```
    ```title="Output"
    Person("John","Doe",25)
    ```

    The `repr(person)` return the string `Person("John","Doe",25)`. If we execute this string, it'll return the `person` object.

When a class doesn't implement the `__str__` method and we pass an instance of that class to the `str()`, Python returns the result of the `__repr__` method because internally the `__str__` method calls the `__repr__` method.

!!! warning "`__str__` vs `__repr__`"
    The `__str__` method returns a string representation of an object that is human-readable while the `__repr__` method returns a string representation of an object that is Python-readable.

## `__eq__`

Implement the Python `__eq__` method to define the equality logic for comparing two objects using the equal operator (`==`)

Python automatically calls the `__eq__` method of a class when you use the `==` operator to compare the instances of the class. By default, Python uses the `is` operator if you don’t provide a specific implementation for the `__eq__` method.

!!! example

    ```py
    class Person:
        def __init__(self, first_name, last_name, age):
            self.first_name = first_name
            self.last_name = last_name
            self.age = age

        def __eq__(self, other):
            if isinstance(other, Person):
                return self.age == other.age

            return False


    john = Person('John', 'Doe', 25)
    jane = Person('Jane', 'Doe', 25)
    mary = Person('Mary', 'Doe', 27)

    print(john == jane)  # True
    print(john == mary)  # False

    print(john == 20)  # False
    ```
    ```title="Output"
    True
    False
    False
    ```

## `__hash__`

The `hash()` function accepts an object and returns the hash value as an integer. When you pass an object to the `hash()` function, Python will execute the `__hash__` special method of the object.

By default, the `__hash__` uses the object's id and the `__eq__` returns `True` if two objects are the same.

If a class overrides the `__eq__` method, Python sets `__hash__` to `None`, the objects of the class become unhashable. We will not able to use them as keys in a dictionary or elements in a set.

!!! example
    
    ```py
    class Person:
        def __init__(self, name, age):
            self.name = name
            self.age = age

        def __eq__(self, other):
            return isinstance(other, Person) and self.age == other.age
    ```

    If we attemp to use the `Person` object in a set, we'll get an error

    ```py
    members = {
        Person('John', 22),
        Person('Jane', 22)
    }
    ```

    ```title="Output"
    TypeError: unhashable type: 'Person'
    ```

    Also, the object loses hasing because if we implement `__eq__`, the `__hash__` is set to `None`.
    
    ```py
    hash(Person('John', 22))
    ```
    
    ```title="Output"
    TypeError: unhashable type: 'Person'
    ```

    To make the `Person` class hashable, we also need to implement the `__hash__` method.
    
    ```py
    class Person:
        def __init__(self, name, age):
            self.name = name
            self.age = age

        def __eq__(self, other):
            return isinstance(other, Person) and self.age == other.age

        def __hash__(self):
            return hash(self.age)
    ```

## `__bool__`

All objects of custom classes return `True` by default. Implement the `__bool__` method to override the default. The `__bool__` method must return either `True` or `False`.

!!! example

    ```py
    class Person:
        def __init__(self, name, age):
            self.name = name
            self.age = age

        def __bool__(self):
            if self.age < 18 or self.age > 65:
                return False
            return True


    if __name__ == '__main__':
        person = Person('Jane', 16)
        print(bool(person))  # False
    ```

    ```title="Output"
    False
    ```

If a custom class doesn't have the `__bool__` method, Python will look for the `__len__()` method. If the `__len__` is zero, the object is `False`. Otherwise, it's `True`.

If a class doesn’t implement the `__bool__` and `__len__` methods, the objects of the class will evaluate to `True`.

<figure markdown="span">
    ![](https://www.pythontutorial.net/wp-content/uploads/2021/02/python-bool.png)
</figure>

!!! example
    The following defines a `Payroll` class that doesn't implement `__bool__` but the `__len__` method:

    ```py
    class Payroll:
        def __init__(self, length):
            self.length = length

        def __len__(self):
            print('len was called...')
            return self.length


    if __name__ == '__main__':
        payroll = Payroll(0)
        print(bool(payroll))  #(1)

        payroll.length = 10
        print(bool(payroll))  #(2)
    ```

    1. In this example payroll’s `__len__` returns `0`, which is `False`:
    2. However, in this example `__len__` returns `10` which is `True`:

    ```title="Output"
    len was called...
    False
    len was called...
    True
    ```

## `__del__`

The garbage collector destroys an object when there is no reference to the object. Python calls the `__del__` method right before the garbage collector destroys the object.

!!! note
    The `__del__` method is not the destructor because the garbage collector destroys the object, not the `__del__` method.

!!! example

    ```py
    class Person:
        def __init__(self, name, age):
            self.name = name
            self.age = age

        def __del__(self):
            print('__del__ was called')


    if __name__ == '__main__':
        person = Person('John Doe', 23)
        person = None #(1)
    ```

    1. `__del__` was called here. When we assign person to `None`, there is no reference to the `Person` object, so Python calls the `__del__` method right before the garbage collector destroys the object.

    ```title="Output"
    __del__ was called
    ```

In practice, we should not use the `__del__` method to clean up the resources. It’s recommended to use the context manager.

If an exception occurs inside the `__del__` method, Python does not raise the exception but keeps it silent.

In practice, we’ll rarely use the `__del__` method.

!!! abstract "Summary"
    - Implement the `__str__` method to customize the string representation of an instance of a class.
    - Typically, the `__repr__()` returns a string that can be executed and yield the same value as the object.
    - The `__str__` method returns a string representation of an object that is human-readable while the `__repr__` method returns a string representation of an object that is Python-readable.
    - Implement the Python `__eq__` method to define the equality logic for comparing two objects using the equal operator (`==`). By default, Python uses the `is` operator if you don’t provide a specific implementation for the `__eq__` method.
    - By default, the `__hash__` uses the object's id.
    - If you implement `__eq__`, Python sets `__hash__` to `None` unless you implement `__hash__`.
    - If a class doesn’t implement the `__bool__` method, Python will use the result of the `__len__` method. If the class doesn’t implement both methods, the objects will be `True` by default.
    - Python calls the `__del__` method right before the garbage collector destroys the object. The garbage collector destroys an object when there is no reference to the object.
    - Exception occurs inside the `__del__` method is not raised but silent.
    - Avoid using `__del__` for clean up resources; use the context manager instead.