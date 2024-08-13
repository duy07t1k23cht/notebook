# What’s are the Differences between Processes and Threads

## Introduction to processes and threads

!!! tip "Definition"

    - A **process** is an instance of a program running on a computer.
    - A **thread** is a unit of execution within a process.

A program may have one or more processes and a process can have one or more threads.

When a program has multiple processes, it's called **multiprocessing**.

If a program has multiple threads, it's called multi **threading**.

In general, programs deal with two types of tasks: I/O-bound or CPU-bound

- **I/O-bound** tasks spend more time doing I/O than doing computations. 

    !!! example
        Network requests, database connections, and file reading/writing.

- **CPU-bound** tasks use more time doing computation than generating I/O requests.
    
    !!! example
        Matrix multiplication, finding prime numbers, video compression, video streaming.

Technically, multithreading is suitable for I/O-bound tasks, and multiprocessing is suitable for CPU-bound tasks.

## The main differences between processes and threads

| Criteria                         | Process                                    | Thread                                                |
|----------------------------------|--------------------------------------------|-------------------------------------------------------|
| Memory Sharing                   | Memory is **not shared** between processes | Memory is **shared** between threads within a process |
| Memory footprint                 | Large                                      | Small                                                 |
| CPU-bound & I/O-bound processing | Optimized for **CPU-bound** tasks          | Optimized for **I/O bound** tasks                     |
| Starting time                    | **Slower** than a thread                   | **Faster** than a process                             |
| Interruptablity                  | Child processes are **interruptible**.     | Threads are **not interruptible**.                    |

!!! abstract "Summary"

    - A **process** is an instance of a program running on a computer.
    - A **thread** is a unit of execution within a process.
    - **A program can have one or more processes** and **a process can have one or more threads**.