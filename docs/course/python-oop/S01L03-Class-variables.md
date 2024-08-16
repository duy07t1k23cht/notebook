# Python Class Variables

## Introduction to the Python class variables

When you define a class using the `class` keyword, Python creates an object with the name the same as the class's name.

??? example
    The following example defines the `HtmlDocument` class and the `HtmlDocument` object:

    ```py
    class HtmlDocument:
        pass
    ```

    The `HtmlDocument` object has the `__name__` property:
    
    ```py
    print(HtmlDocument.__name__)
    ```
    ```title="Output"
    HtmlDocument
    ```

    And the `HtmlDocument` has the type of `type`:

    ```py
    print(type(HtmlDocument))
    print(isinstance(HtmlDocument, type))
    ```
    ```title="Output"
    <class 'type'>
    True
    ```

Class variables are bound to the class. They're shared by all instances of that class.

??? example
    The following example adds the `extension` and `version` class variables to the `HtmlDocument` class:

    ```py
    class HtmlDocument:
        extension = 'html'
        version = '5'
    ```

    Both `extension` and `version` are the class variables of the `HtmlDocument` class. They're bound to the `HtmlDocument` class.

    ```py
    print(HtmlDocument.__dict__)  # (1)
    print(HtmlDocument.extension)
    print(HtmlDocument.version)
    ```

    1. Python stores class variables in the `__dict__` attribute. The `__dict__` is a mapping proxy, which is a dictionary.

        As clearly shown in the output, the class variables `extension` and `version` are in the `__dict__`.

    ```title="Output"
    mappingproxy({
        '__module__': '__main__',
        'extension': 'html',
        'version': 10,
        'render': <function HtmlDocument.render at 0x105107820>,
        '__dict__': <attribute '__dict__' of 'HtmlDocument' objects>,
        '__weakref__': <attribute '__weakref__' of 'HtmlDocument' objects>,
        '__doc__': None
    })
    html
    5
    ```

## Get the values of class variables

Classic way: Use the `.` notation.

Another way: Use the `getattr()` function. The `getattr()` function accepts an object and a variable name. It returns the value of the class variable.

If the variable doesn't exists, we'll get an `AttributeError` exception. To avoid that, we can specify a default value if the class variable doesn't exist.

??? example
    ```py
    extension = getattr(HtmlDocument, 'extension')
    version = getattr(HtmlDocument, 'version')
    media_type = getattr(HtmlDocument, 'media_type', 'text/html')

    print(extension)
    print(version)
    print(media_type)
    ```
    ```title="Output"
    html
    5
    text/html
    ```

## Set values for class variables

Classic way: Use the `.` notation.

Another way: Use the `setattr()` function.

??? example
    ```py
    setattr(HtmlDocument, media_type, 'text/html')
    print(HtmlDocument.media_type)
    ```
    ```title="Output"
    text/html
    ```

## Delete class variables

Use `delattr()` function or the `del` keyword

??? example
    ```py
    del HtmlDocument.extension
    delattr(HtmlDocument, "version")
    print(HtmlDocument.__dict__)
    ```
    ```title="Output"
    mappingproxy({
        '__module__': '__main__',
        'render': <function HtmlDocument.render at 0x101317790>,
        '__dict__': <attribute '__dict__' of 'HtmlDocument' objects>,
        '__weakref__': <attribute '__weakref__' of 'HtmlDocument' objects>,
        '__doc__': None
    })
    ```
    As shown, there is no longer `extension` and `version` attribute.

## Class variable storage

Python stores class variables in the `__dict__` attribute. The `__dict__` is a mapping proxy, which is a dictionary.

??? example
    ```py
    class HtmlDocument:
        extension = 'html'
        version = '5'

    HtmlDocument.media_type = 'text/html'

    print(HtmlDocument.__dict__)
    ```
    ```hl_lines="3 4 8"title="Output"
    mappingproxy({
        '__module__': '__main__',
        'extension': 'html',
        'version': '5',
        '__dict__': <attribute '__dict__' of 'HtmlDocument' objects>,
        '__weakref__': <attribute '__weakref__' of 'HtmlDocument' objects>,
        '__doc__': None,
        'media_type': 'text/html'
    })
    ```

    As clearly shown in the output, the `__dict__` has three class variables: `extension`, `version`, and `media_type` besides other predefined class variables.

Python does not allow you to change the `__dict__` directly.

??? example
    The following result in an error:
    ```py
    HtmlDocument.__dict__['released'] = 2008
    ```
    ```title="Output"
    TypeError: 'mappingproxy' object does not support item assignment
    ```

Python allows to access class variables through the `__dict__` dictionary, it's not a good practice.

## Callable class attributes

Class attributes can by any object such as a function.

When we add a function to a class, the function becomes a class attribute. Since a function is callable, the class attribute is called a callable class attribute.

??? example
    ```py
    class HtmlDocument:
        extension = "html"
        version = "5"

        def render():
            print("Rendering the Html doc...")


    print(HtmlDocument.__dict__)
    HtmlDocument.render()
    ```
    ```hl_lines="5" title="Output"
    mappingproxy({
        '__module__': '__main__',
        'extension': 'html',
        'version': '5',
        'render': <function HtmlDocument.render at 0x10508a8b0>,
        '__dict__': <attribute '__dict__' of 'HtmlDocument' objects>,
        '__weakref__': <attribute '__weakref__' of 'HtmlDocument' objects>,
        '__doc__': None
    })
    Rendering the Html doc...
    ```
    In this example, the `render` is a class attribute of the `HtmlDocument` class. Its value is a function.

!!! abstract "Summary"
    - Class variables are attributes of the class object.
    - Use `.` notation or `getattr()` function to get the value of a class attribute.
    - Use `.` notation or `setattr()` function to set the value of a class attribute.
    - Python is a dynamic language. Therefore, you can assign a class variable to a class at runtime.
    - Python stores class variables in the `__dict__` attribute. The `__dict__` attribute is a dictionary.