# Python Property

!!! example

    ```py
    from pprint import pprint

    class Person:
        def __init__(self, name, age):
            self.name = name
            self.age = age

        def set_age(self, age):
            if age <= 0:
                raise ValueError('The age must be positive')
            self._age = age

        def get_age(self):
            return self._age

        age = property(fget=get_age, fset=set_age)


    print(Person.age)

    john = Person('John', 18)
    pprint(john.__dict__)

    john.age = 19
    pprint(Person.__dict__)
    ```
    
    ```title="Output"
    <property object at 0x100c36db0>
    {'_age': 18, 'name': 'John'}
    mappingproxy({'__dict__': <attribute '__dict__' of 'Person' objects>,
                '__doc__': None,
                '__init__': <function Person.__init__ at 0x100c67670>,
                '__module__': '__main__',
                '__weakref__': <attribute '__weakref__' of 'Person' objects>,
                'age': <property object at 0x100c36db0>,
                'get_age': <function Person.get_age at 0x100e3ce50>,
                'set_age': <function Person.set_age at 0x100e3cdc0>})
    ```

!!! abstract "Summary"
    - Use the Python `property()` class to define a property for a class.