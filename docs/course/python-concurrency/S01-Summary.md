# Section Summary - Multithreading

## Process vs Thread

|                                  | Process                                                            | Thread                                                |
|----------------------------------|--------------------------------------------------------------------|-------------------------------------------------------|
| Definition                       | A **process** is an instance of a program running on a computer.   | A **thread** is a unit of execution within a process. |
| Memory Sharing                   | Memory is **not shared** between processes                         | Memory is **shared** between threads within a process |
| Memory footprint                 | Large                                                              | Small                                                 |
| CPU-bound & I/O-bound processing | Optimized for **CPU-bound** tasks                                  | Optimized for **I/O bound** tasks                     |
| Starting time                    | **Slower** than a thread                                           | **Faster** than a process                             |
| Interruptablity                  | Child processes are **interruptible**.                             | Threads are **not interruptible**.                    |

## `threading` module

- Use `Thread(function, args)` to create a new thread.
- Call the `start()` method of the `Thread` class to start the thread.
- Call the `join()` method of the `Thread` class to wait for the thread to complete in the main thread.
- Extend the `Thread` class to run code in a new thread by calling the `__init__()` method of the superclass in the subclass and override the `run` method to add the code to run in a new thread.
- Extend the `Thread` class and set the instance variables inside the subclass to return the values from a child thread to the main thread.

## Daemon Threads

**Daemon threads** are background threads. In other words, daemon threads execute tasks in the background.

!!! example
    - Log information to a file in the background
    - Scrap contents from a website in the background
    - Auto-save the data into a database in the background

The program can exit and doesn't need to wait for the daemon threads to be completed. A daemon thread is automatically killed when the program exits.

## Thread Pools

A **thread pool** is a "collection" of worker threads that are used to execute tasks concurrently.

The `ThreadPoolExecutor` class extends the `Executor` class and returns a `Future` object. Use context manager when working with thread pool.