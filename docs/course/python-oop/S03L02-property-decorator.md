# Python Property Decorator

Use decorators to create a property using the following pattern:

```py
class MyClass:
    def __init__(self, attr):
        self.prop = attr

    @property
    def prop(self):
        return self.__attr

    @prop.setter
    def prop(self, value):
        self.__attr = value
```

!!! example

    ```py
    class Person:
        def __init__(self, name, age):
            self.name = name
            self.age = age

        @property
        def age(self):
            return self._age

        @age.setter
        def age(self, value):
            if value <= 0:
                raise ValueError('The age must be positive')
            self._age = value

        @property
        def name(self):
            return self._name

        @name.setter
        def name(self, value):
            if value.strip() == '':
                raise ValueError('The name cannot be empty')
            self._name = value
    ```

Underhood, the `@property` decorator uses the `property` class that has three methods: setter, getter, and deleter.

!!! abstract "Summary"
    - User the `@property` decorator to create a property for a class.