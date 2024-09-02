# Python Multiprocessing

Multiprocessing allows two or more processors to simultaneously process two or more different parts of a program.

In Python, we use the `multiprocessing` module to implement multiprocessing.

!!! example

    ```py
    import time
    import multiprocessing


    def task() -> int:
        result = 0
        for _ in range(10**8):
            result += 1
        return result


    if __name__ == "__main__":
        start = time.perf_counter()

        # Create two processes and pass the task function to each (1)
        p1 = multiprocessing.Process(target=task)
        p2 = multiprocessing.Process(target=task)

        # Start the processes
        p1.start()
        p2.start()

        # Wait for the processes to complete
        p1.join()
        p2.join()

        finish = time.perf_counter()
        print(f"It took {finish-start:.2f} second(s) to finish")
    ```

    1.  Note that the `Process()` constructor returns a new `Process` object.

    ```title="Output"
    It took 2.29 second(s) to finish
    ```

!!! abstract "Summary"

    - Use Python multiprocessing to run code in parallel to deal with CPU-bound tasks.
