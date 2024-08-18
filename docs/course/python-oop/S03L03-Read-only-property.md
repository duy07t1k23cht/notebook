# Python Readonly Property

## Cache calculated properties

!!! example
    
    ```py
    import math

    class Circle:
        def __init__(self, radius):
            self._radius = radius
            self._area = None  #(1)

        @property
        def radius(self):
            return self._radius

        @radius.setter
        def radius(self, value):
            if value < 0:
                raise ValueError('Radius must be positive')

            if value != self._radius:
                self._radius = value
                self._area = None  #(2)

        @property
        def area(self):
            if self._area is None:
                self._area = math.pi * self.radius ** 2  #(3)

            return self._area
    ```

    1. The `_area` attribute is the cache that stores the calculated area.
    2. Reset the cache by setting the `_area` to `None`.
    3. The `area` property returns `_area` if it is not `None`. Otherwise, calculate the area, save it into the `_area`, and return it.

!!! abstract "Summary"
    - Define only the getter to make a property readonly.
    - Do use computed property to make the property of a class more natural.
    - Use caching computed properties to improve the performance.