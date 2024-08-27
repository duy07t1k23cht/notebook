# How to use the Python Threading Lock to Prevent Race Conditions

## What is a race condition

A **race condition** occurs when two or more threads try to access a shared variable simultaneously, leading to unpredictable outcomes.

!!! example

    The following example illustrates a race condition:

    ```py
    from threading import Thread
    from time import sleep

    counter = 0

    def increase(by):
        global counter

        local_counter = counter
        local_counter += by

        sleep(0.1)  #(1)

        counter = local_counter
        print(f'counter={counter}')

    # create threads
    t1 = Thread(target=increase, args=(10,))  #(2)
    t2 = Thread(target=increase, args=(20,))  #(3)

    # start the threads
    t1.start()
    t2.start()

    # wait for the threads to complete
    t1.join()
    t2.join()

    print(f'The final counter is {counter}')
    ```

    1. Sleep to ensure that when both threads start, the `counter` still `0`.
    2. Increase the `counter` by `10`.
    3. Increase the `counter` by `20`.

    If the thread `t1` completes before the thread `t2`, we'll see the following output:
    
    ```title="Output"
    counter=10
    counter=20
    The counter is 20
    ```

    Otherwise, we'll see the following output:

    ```title="Output"
    counter=20
    counter=10
    The final counter is 10
    ```

## Using a threading lock to prevent the race condition

A **thread lock** is a synchronization primitive that provides exclusive access to a shared resource in a multithreaded application. A thread lock is also known as a **mutex** which is short for mutual exclusion.

A threading lock has two states: _locked_ and _unlocked_.

When a thread acquires a lock, the lock enters the locked state. The thread can have exclusive access to the shared resource. Other threads that attemp to acquire the lock while it is locked will be blocked and wait until the lock is released.

In Python, we can use the `Lock` class from the `threading` module to create a lock object:

First, create an instance of the `Lock` class:

```py
lock = Lock()
```

Second, acquire a lock by calling the `acquire()` method.

```py
lock.acquire()
```

Third, release the lock once the thread completes changing the shared variable:

```py
lock.release()
```

!!! example

    The following example shows how to use the `Lock` object to prevent the race condition in the previous program:

    ```py
    from threading import Thread, Lock
    from time import sleep

    counter = 0

    def increase(by, lock):
        global counter

        lock.acquire()  #(1)

        local_counter = counter
        local_counter += by

        sleep(0.1)

        counter = local_counter
        print(f'counter={counter}')

        lock.release()  #(2)

    lock = Lock()

    # create threads
    t1 = Thread(target=increase, args=(10, lock))
    t2 = Thread(target=increase, args=(20, lock))

    # start the threads
    t1.start()
    t2.start()
 
    # wait for the threads to complete
    t1.join()
    t2.join()

    print(f'The final counter is {counter}')
    ```

    1. Acquire a lock before accessing the `counter` variable.
    2. Release the lock after updating the new value for the `counter`.

## Using the threading lock with the `with` statement

It's easier to use the lock object with the `with` statement to acquire and release the lock within a block of code:

```py
import threading

# Create a lock object
lock = threading.Lock()

# Perform some operations within a critical section
with lock:
    # Lock was acquired within the with block
    # Perform operations on the shared resource
    # ...

# the lock is released outside the with block
```

!!! example

    ```py
    from threading import Thread, Lock
    from time import sleep

    counter = 0

    def increase(by, lock):
        global counter

        with lock:
            local_counter = counter
            local_counter += by

            sleep(0.1)

            counter = local_counter
            print(f'counter={counter}')

    lock = Lock()

    # create threads
    t1 = Thread(target=increase, args=(10, lock))
    t2 = Thread(target=increase, args=(20, lock))

    # start the threads
    t1.start()
    t2.start()

    # wait for the threads to complete
    t1.join()
    t2.join()

    print(f'The final counter is {counter}')
    ```

!!! abstract "Summary"

    -   A race condition occurs when two threads access a shared variable at the same time.
    -   Use a threading lock object to prevent the race condition.
    -   Call the `acquire()` method of a lock object to acquire a lock.
    -   Call the `release()` method of a lock object to release the previous acquired lock.
    -   Use a threading lock object with the `with` statement to make it easier to acquire and release the lock.