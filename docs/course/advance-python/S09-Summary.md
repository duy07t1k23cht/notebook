# Section Summary - Sequence Types

A custom sequence type needs to implement the following methods:

-   `__getitem__`
    -   Has the `index` argument which should be either an integer or a slice object.
    -   If the `index` is out of bounds, the `__getitem__` method should raise an `IndexError` exception.

-   `__len__`
    -   Returns the length of the sequence.

!!! example

    ```py
    class Sentence:
        def __init__(self, sentence: str) -> None:
            self.sentence = sentence
            self.words = sentence.split()

        def __len__(self):
            return len(self.words)

        def __getitem__(self, index):
            if isinstance(index, int):
                if index > len(self.words):
                    raise IndexError
                return self.words[index]

            elif isinstance(index, slice):
                indices = index.indices(len(self.words))
                return " ".join([self.words[i] for i in range(*indices)])

            else:
                raise TypeError


    sentence = Sentence("I love you 3000")

    print(sentence[-1])
    print(sentence[1:3])
    print(sentence[5])
    ```

    ```title="Output"
    3000
    love you
    Traceback (most recent call last):
    File "/Users/duynm/duynm/me/studying/AdvancePython/Section 09. Sequence Types/test.py", line 27, in <module>
        print(sentence[5])
    File "/Users/duynm/duynm/me/studying/AdvancePython/Section 09. Sequence Types/test.py", line 12, in __getitem__
        raise IndexError
    IndexError
    ```

!!! abstract "Summary"
    -   Implement the `__len__` and `__getitem__` method to define a custom sequence.
    -   The `__getitem__` method need to returns an element based on a given index or raise an `IndexError` if the index is out of bounds.