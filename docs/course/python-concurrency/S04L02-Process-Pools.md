# Python ProcessPoolExecutor

Like a thread pool, a process pool is a pattern for managing processes automatically.

The `ProcessPoolExecutor` class from the `concurrent.futures` module allows to create and manage a process pool.

The `ProcessPoolExecutor` extends the `Executor` class that has three methods:

-   `submit()`: dispatch a function to be executed and return a `Future` object. The `submit()` method takes a function and executes it asynchronously.
-   `map()`: execute a function asynchronously for each element in an iterable.
-   `shutdown()`: shut down the executor.

To release the resources held by the executor, we need to call the `shutdown()` method explicitly. To shut down the executor automatically, we can use a context manager.

A `Future` is an object that represents the eventual result of an asynchronous operation.

The `Future` class has two useful methods:

-   `result()`: return the result of an asynchronous operation.
-   `exception()`: return the exception of an asynchronous operation in case an exception occurs.

!!! example

    ```py
    import time
    import os
    from PIL import Image, ImageFilter

    from concurrent.futures import ProcessPoolExecutor

    filenames = [
        'images/1.jpg',
        'images/2.jpg',
        'images/3.jpg',
        'images/4.jpg',
        'images/5.jpg',
    ]

    def create_thumbnail(filename, size=(50,50), thumb_dir ='thumbs'):
        # open the image
        img = Image.open(filename)
        
        # apply the gaussian blur filter
        img = img.filter(ImageFilter.GaussianBlur())

        # create a thumbnail
        img.thumbnail(size)
        
        # save the image
        img.save(f'{thumb_dir}/{os.path.basename(filename)}')

        # display a message
        print(f'{filename} was processed...')

    def main():
        start = time.perf_counter()

        # Create a process pool
        with ProcessPoolExecutor() as executor:
            executor.map(create_thumbnail, filenames)
    
        finish = time.perf_counter()

        print(f'It took {finish-start: .2f} second(s) to finish')

    if __name__ == '__main__':
        main()
    ```

!!! abstract "Summary"

    -   Use the Python `ProcessPoolExecutor` class t o create and manage a process pool automatically.