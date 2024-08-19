# Float

!!! abstract "Summary"

    -   Python use a fixed number of bytes (8 bytes) to represent floats. Therefore, it can present some numbers in binary approximately.
    -    Use `isclose()` function from the `math` module to test equality for floating-point numbers.

        !!! example

            ```py
            from math import isclose

            x = 0.1 + 0.1 + 0.1
            y = 0.3

            print(isclose(x,y))
            ```
    -   float to int:
        <figure markdown="span">
            ![](https://thomasjfrank.com/wp-content/uploads/2023/03/Rounding-Functions.jpg){ width="70%" }
        </>
    -   Use the `round(number, ndigits)` function to round a `number` to the `digits` precision after the decimal point.

        !!! example
            
            ```py
            round(2/3, 3)
            ```
            ```title="Output"
            0.667
            ```