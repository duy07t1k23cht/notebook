# Python ThreadPoolExecutor

## Introduction to the Python ThreadPoolExecutor class

The `Thread` class is useful when you want to create threads manually. However, manually managing threads is not efficient because creating and destroying many threads frequently is very expensive in terms of computational costs.

Instead, we may want to reuse the threads if we expect to run many ad-hoc tasks in the program.

A thread pool allows us to achieve this.

### Thread pool

A thread pool allows us to automatically manage a pool of threads efficiently.

![](https://www.pythontutorial.net/wp-content/uploads/2022/05/Python-ThreadPoolExecutor-Thread-Pool.svg)

Each thread in the pool is called a worker thread or a worker.

!!! tip "Definition"
    A **thread pool** is a "collection" of worker threads that are used to execute tasks concurrently.

A thread pool allows you to reuse the worker threads once the tasks are completed.

Whenever we want a thread we pick one from this pool and delegate a task to it. Once the task is complete, we add the thread back to the thread pool.

!!! example
    - When a web server spins up, it creates a thread pool of `n`.
    - When a client connects to the web server, we pick one thread to handle the request.
    - Once the response is generated and sent, the thread is added back to the pool.

Typically, a thread pool allows you to configure the number of worker threads and provides a specific naming convention for each worker thread.

To create a thread pool, we use the `ThreadPoolExecutor` class from the `concurrent.futures` module.

### Executor

The `Executor` class has three methods to control the thread pool:

- `submit()`: dispatch a function to be executed and return a `Future` object. The `submit()` method takes a function and executes it asynchronously.
- `map()`: execute a function asynchronously for each element in an iterable.
- `shutdown()`: shut down the executor.

The `ThreadPoolExecutor` class extends the `Executor` class and returns a `Future` object.

When you create a new instance of the `ThreadPoolExecutor` class, Python starts the `Executor`.

Once we complete working with the executor, we must explicitly call the `shutdown()` method to release the resources held by the executor. To avoid calling the `shutdown()` method explicitly, we can use the context manager.

### Future object

A `Future` is an object that represents the eventual result of an asynchronous operation.

The `Future` class has two useful methods:

- `result()`: return the result of an asynchronous operation.
- `exception()`: return the exception of an asynchronous operation in case an exception occurs.

## Python `ThreadPoolExecutor` examples

The following program uses a single thread:

```py
from time import sleep, perf_counter

def task(id):
    print(f'Starting the task {id}...')
    sleep(1)
    return f'Done with task {id}'

start = perf_counter()

print(task(1))
print(task(2))

finish = perf_counter()

print(f"It took {finish-start} second(s) to finish.")
```
```title="Output"
Starting the task 1...
Done with task 1
Starting the task 2...
Done with task 2
It took 2.0144479 second(s) to finish.
```

### Using the `submit()` method example

```py
from time import sleep, perf_counter
from concurrent.futures import ThreadPoolExecutor

def task(id):
    print(f'Starting the task {id}...')
    sleep(1)
    return f'Done with task {id}'

start = perf_counter()

with ThreadPoolExecutor() as executor:  #(1)
    f1 = executor.submit(task, 1)  #(2)
    f2 = executor.submit(task, 2)

    print(f1.result())  #(3)
    print(f2.result())

finish = perf_counter()

print(f"It took {finish-start} second(s) to finish.")
```

1. Create a thread pool using the `ThreadPoolExecutor` using a context manager.
2. Calling the `task()` function by passing it to the `submit()` method of the executor. The `submit()` method returns a `Future` object.
    
    In this example, we have two `Future` objects: `f1` and `f2`.

3. To get the result from the `Future` object, we called its `result()` method.

```title="Output"
Starting the task 1...
Starting the task 2...
Done with task 1
Done with task 2
It took 1.0177214 second(s) to finish.
```

### Using the `map()` method example

```py
from time import sleep, perf_counter
from concurrent.futures import ThreadPoolExecutor


def task(id):
    print(f'Starting the task {id}...')
    sleep(1)
    return f'Done with task {id}'

start = perf_counter()

with ThreadPoolExecutor() as executor:
    results = executor.map(task, [1, 2])  #(1)
    for result in results:  #(2)
        print(result)

finish = perf_counter()

print(f"It took {finish-start} second(s) to finish.")
```

1. Run the `task()` function for each `id` in the list `[1, 2]`. The `map()` method returns an iterator that contains the result of the function calls.
2. Iterate over the results and print them out.

## Python `ThreadPoolExecutor` practical example

The following program downloads multiple images from Wikipedia using a thread pool:

```py
from concurrent.futures import ThreadPoolExecutor
from urllib.request import urlopen
import time
import os

def download_image(url):  #(1)
    image_data = None
    with urlopen(url) as f:
        image_data = f.read()

    if not image_data:
        raise Exception(f"Error: could not download the image from {url}")

    filename = os.path.basename(url)
    with open(filename, 'wb') as image_file:
        image_file.write(image_data)
        print(f'{filename} was downloaded...')

start = time.perf_counter()

urls = ['https://upload.wikimedia.org/wikipedia/commons/9/9d/Python_bivittatus_1701.jpg',
        'https://upload.wikimedia.org/wikipedia/commons/4/48/Python_Regius.jpg',
        'https://upload.wikimedia.org/wikipedia/commons/d/d3/Baby_carpet_python_caudal_luring.jpg',
        'https://upload.wikimedia.org/wikipedia/commons/f/f0/Rock_python_pratik.JPG',
        'https://upload.wikimedia.org/wikipedia/commons/0/07/Dulip_Wilpattu_Python1.jpg']

with ThreadPoolExecutor() as executor: #(2)
      executor.map(download_image, urls)

finish = time.perf_counter()    

print(f'It took {finish-start} second(s) to finish.')
```

1. Define a function `download_image()` that downloads an image from an URL and saves it into a file.
2. Execute the `download_image()` function using a thread pool by calling the `map()` method of the `ThreadPoolExecutor` object.

!!! abstract "Summary"
    - A thread pool is a pattern for managing multiple threads efficiently.
    - Use `ThreadPoolExecutor` class to manage a thread pool in Python.
    - Call the `submit()` method of the `ThreadPoolExecutor` to submit a task to the thread pool execution. The `submit()` method returns a `Future` object.
    - Call the `map()` method of the `ThreadPoolExecutor` class to execute a function in a thread pool with each element in a list.