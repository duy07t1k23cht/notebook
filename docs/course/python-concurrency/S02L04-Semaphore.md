# Python Semaphore

## Introduction to the Python Semaphore

A Python **semaphore** is a synchronization primitive that allows to control access to a shared resource.

A semaphore maintains a count. When a thread wants to access the shared resource, the semaphore checks the count.

A semaphore has two main operations:

-   **Acquire**: checks the count and decreses it if it is greater than zero. If the count is zero, the semaphore will block the thread until another thread releases the semaphore.
-   **Release**: increments the count that allow other threads to acquire it.

## Using a Python semaphore

!!! example

    ```py
    import threading
    import time
    import random

    MAX_CONCURRENT_THREAD = 3
    semaphore = threading.Semaphore(MAX_CONCURRENT_THREAD)  #(1)


    def task(id):
        with semaphore:  #(2)
            print("Performing task", id)
            time.sleep(3 + random.random())  # Simulate some work
            print("Finish task", id)


    if __name__ == "__main__":
        threads = []

        for i in range(10):
            t = threading.Thread(target=task, args=(i,))
            threads.append(t)
            t.start()

        [t.join() for t in threads]
    ```

    1. Create a `Semaphore` object that only allows up to three threads to acquire it at the same time.
    2. Use context manager to ensure a semaphore is properly acquired and released, even if exceptions occur during running the critical section of a code.

    ```title="Output"
    Performing task 0
    Performing task 1
    Performing task 2
    Finish task 1
    Performing task 3
    Finish task 2
    Performing task 4
    Finish task 0
    Performing task 5
    Finish task 4
    Performing task 6
    Finish task 3
    Performing task 7
    Finish task 5
    Performing task 8
    Finish task 6
    Performing task 9
    Finish task 7
    Finish task 8
    Finish task 9
    ```

    The output shows that only a maximum of three threads can be executed at the same time.

??? question "_semaphore_ vs _thread pool_"

    -   **Semaphore** is about access control to resources.
    -   **Thread pool** is about task management and parallel execution.

!!! abstract "Summary"

    -   Use Python semaphore to control the number of threads that can access a shared resource simultaneously.