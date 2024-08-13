# Python Threading

## Single-threaded applications

Let's start with a simple program:

```py 
from time import sleep, perf_counter

def task():
    print('Starting a task...')
    sleep(1)
    print('done')


start_time = perf_counter()

task()
task()

end_time = perf_counter()

print(f'It took {end_time- start_time: 0.2f} second(s) to complete.')
```
```title="Output"
Starting a task...
done
Starting a task...
done
It took 2.01 second(s) to complete.
```

The following diagram illustrates how the program works:

<figure markdown="span">
    ![Image title](https://www.pythontutorial.net/wp-content/uploads/2020/12/Python-Threading-Single-threaded-App.png){ width="70%" }
</figure>

This program has one process with single thread, which is called the **main thread**.

Because the program has only one thread, it's called a single-threaded program.

## Multi-threaded program example

We will create a multi-threaded program.

First, import the Thread class from the threading module.

```py 
from threading import Thread
```

Then, create a new thread by instantiating an instance of the Thread class:

```py 
new_thread = Thread(target=fn,args=args_tuple)
```

The two main parameters of `Thread()` are:

-   `target`: the function (`fn`) to run in the new thread.
-   `args`: the arguments of the function (`fn`). The `args` is a tuple.

Third, start the thread by calling `start()` method of `Thread` instance:

```py 
new_thread.start()
```

Call the `join()` method if we want to wait for the thread to complete in the main thread.

```py 
new_thread.join()
```

The following program illustrates how to use the `threading` module:

```py 
from time import sleep, perf_counter
from threading import Thread


def task():
    print('Starting a task...')
    sleep(1)
    print('done')


start_time = perf_counter()

# create two new threads
t1 = Thread(target=task)
t2 = Thread(target=task)

# start the threads
t1.start()
t2.start()

# wait for the threads to complete
t1.join()
t2.join()

end_time = perf_counter()

print(f'It took {end_time- start_time: 0.2f} second(s) to complete.')
```
```title="Output"
Starting a task...
Starting a task...
done
done
It took  1.00 second(s) to complete.
```

When the program executes, it'll have three threads: The main thread and two other child threads.

The following diagram shows how threads execute:

<figure markdown="span">
    ![Image title](https://www.pythontutorial.net/wp-content/uploads/2020/12/Python-Threading-Multi-threaded-App.png){ width="70%" }
</figure>

## Passing arguments to threads

The following program shows how to pass arguments to the function assigned to a thread:

```py
from time import sleep, perf_counter
from threading import Thread


def task(id):
    print(f'Starting the task {id}...')
    sleep(1)
    print(f'The task {id} completed')


start_time = perf_counter()

# create and start 10 threads
threads = []
for n in range(1, 11):
    t = Thread(target=task, args=(n,))
    threads.append(t)
    t.start()

# wait for the threads to complete
for t in threads:
    t.join()

end_time = perf_counter()

print(f'It took {end_time- start_time: 0.2f} second(s) to complete.')
```
```title="Output"
Starting the task 1...
Starting the task 2...
Starting the task 3...
Starting the task 4...
Starting the task 5...
Starting the task 6...
Starting the task 7...
Starting the task 8...
Starting the task 9...
Starting the task 10...
The task 10 completed
The task 8 completed
The task 1 completed
The task 6 completed
The task 7 completed
The task 9 completed
The task 3 completed
The task 4 completed
The task 2 completed
The task 5 completed
It took  1.02 second(s) to complete.
```
!!! note
    The program doesn't execute the thread in the order from 1 to 10.

??? question "Why do we have to call the `join()` methods in a separate loop?"

    Have a look at the program:
    ```py
    # create and start 10 threads
    threads = []
    for n in range(1, 11):
        t = Thread(target=task, args=(n,))
        threads.append(t)
        t.start()
        t.join()
    ```
    Here, we create, start, and immediately join each thread within the loop. This causes each thread to run sequentially instead of concurrently because the program waits for each thread to finish (`t.join()`) before starting a new one.

## When to use Python threading

Python threading is optimized for I/O-bound tasks. For example, requesting remote resources, connecting a database server, or reading and writing files.

## A Practical Python threading example

The following multi-threaded program shows how to replace a substring with the new one in the text files:

```py
from threading import Thread
from time import perf_counter


def replace(filename, substr, new_substr):
    print(f'Processing the file {filename}')
    # get the contents of the file
    with open(filename, 'r') as f:
        content = f.read()

    # replace the substr by new_substr
    content = content.replace(substr, new_substr)

    # write data into the file
    with open(filename, 'w') as f:
        f.write(content)


def main():
    filenames = [
        'c:/temp/test1.txt',
        'c:/temp/test2.txt',
        'c:/temp/test3.txt',
        'c:/temp/test4.txt',
        'c:/temp/test5.txt',
        'c:/temp/test6.txt',
        'c:/temp/test7.txt',
        'c:/temp/test8.txt',
        'c:/temp/test9.txt',
        'c:/temp/test10.txt',
    ]

    # create threads
    threads = [Thread(target=replace, args=(filename, 'id', 'ids'))
            for filename in filenames]

    # start the threads
    for thread in threads:
        thread.start()

    # wait for the threads to complete
    for thread in threads:
        thread.join()


if __name__ == "__main__":
    start_time = perf_counter()

    main()

    end_time = perf_counter()
    print(f'It took {end_time- start_time :0.2f} second(s) to complete.')
```
```title="Output"
Processing the file c:/temp/test1.txt
Processing the file c:/temp/test2.txt
Processing the file c:/temp/test3.txt
Processing the file c:/temp/test4.txt
Processing the file c:/temp/test5.txt
Processing the file c:/temp/test6.txt
Processing the file c:/temp/test7.txt
Processing the file c:/temp/test8.txt
Processing the file c:/temp/test9.txt
Processing the file c:/temp/test10.txt
It took 0.02 second(s) to complete.
```

!!! abstract "Summary"

    - Use the Python `threading` module to create a multi-threaded application.
    - Use `Thread(function, args)` to create a new thread.
    - Call the `start()` method of the `Thread` class to start the thread.
    - Call the `join()` method of the `Thread` class to wait for the thread to complete in the main thread.
    - Only use threading for I/O bound processing applications.