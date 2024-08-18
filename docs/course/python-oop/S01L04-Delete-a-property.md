# Python Delete Property

To create a property of a class, you use the `@property` decorator. Underhood, the `@property` decorator uses the `property` class that has three methods: setter, getter, and deleter.

Use deleter to delete a property from an object.

!!! note
    The `deleter()` method deletes a property of an object, not a class.

!!! example

    ```py
    class Person:
        def __init__(self, name):
            self._name = name

        @property
        def name(self):
            return self._name

        @name.setter
        def name(self, value):
            if value.strip() == "":
                raise ValueError("name cannot be empty")
            self._name = value

        @name.deleter
        def name(self):
            del self._name

    print(Person.__dict__)
    ```

    ```py hl_lines="4" title="Output"
    mappingproxy({
        '__module__': '__main__',
        '__init__': <function Person.__init__ at 0x103047700>,
        'name': <property object at 0x102f315e0>,
        '__dict__': <attribute '__dict__' of 'Person' objects>,
        '__weakref__': <attribute '__weakref__' of 'Person' objects>,
        '__doc__': None
    })
    ```

    The `Person.__dict__` class has the `name` variable.

    The following creates a new instance of the `Person` class:

    ```py
    person = Person('John')
    print(person.__dict__)
    ```

    ```title="Output"
    {'_name': 'John'}
    ```

    The following uses the `del` keyword to delete the `name` property:

    ```py
    del person.name  #(1)

    print(Person.__dict__)
    print(person.__dict__)
    ```

    1. Internally, Python will execute the `deleter` method that deletes the `_name` attribute from the `person` object. The `person.__dict__` will be empty like this:

    ```py
    mappingproxy({
        '__module__': '__main__',
        '__init__': <function Person.__init__ at 0x102667700>,
        'name': <property object at 0x102551540>,
        '__dict__': <attribute '__dict__' of 'Person' objects>,
        '__weakref__': <attribute '__weakref__' of 'Person' objects>,
        '__doc__': None
    })
    {}
    ```

    And if we attempt to access `name` property again, we'll get an error:

    ```py
    print(person.name)
    ```

    ```title="Output"
    AttributeError: 'Person' object has no attribute '_name'
    ```

!!! abstract "Summary"
    - Use the deleter decorator to delete a property of an instance of a class.