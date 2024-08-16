# Python Instance Variables

!!! abstract "Summary"
    - Instance variables are bound to a specific instance of a class.

    - Python stores instance variables in the `__dict__` attribute of the instance. Each instance has its own `__dict__` attribute and the keys in this `__dict__` may be different.
        
        !!! example
            ```py
            class HtmlDocument:
                version = 5
                extension = "html"

                def __init__(self, name, contents):
                    self.name = name
                    self.contents = contents


            document = HtmlDocument("Blank", "")

            print(HtmlDocument.__dict__)
            print(document.__dict__)
            ```
            ```py title="Output"
            mappingproxy({
                '__module__': '__main__',
                'version': 5,
                'extension': 'html',
                '__init__': <function HtmlDocument.__init__ at 0x1014ae8b0>,
                '__dict__': <attribute '__dict__' of 'HtmlDocument' objects>,
                '__weakref__': <attribute '__weakref__' of 'HtmlDocument' objects>,
                '__doc__': None
            })
            {'name': 'Blank', 'contents': ''}
            ```

    - When you access a variable via the instance, Python finds the variable in the `__dict__` attribute of the instance. If it cannot find the variable, it goes up and look it up in the `__dict__` attribute of the class.

        !!! example
            ```py
            class HtmlDocument:
                version = 5
                extension = "html"

                def __init__(self, name, contents):
                    self.name = name
                    self.contents = contents


            document = HtmlDocument("Blank", "")
            document.version = 10

            print(HtmlDocument.__dict__)
            print(document.__dict__)

            print(document.version, HtmlDocument.version)
            ```
            ```py title="Output"
            mappingproxy({
                '__module__': '__main__',
                'version': 5,
                'extension': 'html',
                '__init__': <function HtmlDocument.__init__ at 0x1014ae8b0>,
                '__dict__': <attribute '__dict__' of 'HtmlDocument' objects>,
                '__weakref__': <attribute '__weakref__' of 'HtmlDocument' objects>,
                '__doc__': None
            })
            {'name': 'Blank', 'contents': '', 'version': 10}
            10 5
            ```