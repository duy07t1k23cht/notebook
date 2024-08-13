# Python Daemon Threads

## Introduction to the Python daemon threads

Sometimes, you may want to execute a task in the background. To do that you use a special kind of thread called a daemon thread.

!!! tip "Definition"

    **Daemon threads** are background threads. In other words, daemon threads execute tasks in the background.

Daemon threads are helpful for executing tasks that support non-daemon threads in the program.

!!! example
    - Log information to a file in the background
    - Scrap contents from a website in the background
    - Auto-save the data into a database in the background

## Creating a daemon thread

Two ways to create a daemon thread:

```py
t = Thread(target=f, deamon=True)
```
```py
t = Thread(target=f)
t.deamon = True
```

## A daemon thread example

The following example shows how to create a non-daemon thread that shows the number of seconds that the program has been waiting for:

```py
from threading import Thread
import time


def show_timer():
    count = 0
    while True:
        count += 1
        time.sleep(1)
        print(f'Has been waiting for {count} second(s)...')


t = Thread(target=show_timer)
t.start()

answer = input('Do you want to exit?\n')
```

If we run the program, it'll show the following output and run forever.

```title="Output"
Do you want to exit?Has been waiting for 1 second(s)...
Has been waiting for 2 second(s)...
Has been waiting for 3 second(s)...
Has been waiting for 4 second(s)...
Y
Has been waiting for 5 second(s)...
Has been waiting for 6 second(s)...
```

To terminate the program, you need to kill the terminal.

The program runs indefinitely because the thread `t` is a non-daemon thread. The program needs to wait for all non-daemon threads to complete before it can exit.

Now, let's turn the thread into a daemon thread:

```py
from threading import Thread
import time


def show_timer():
    count = 0
    while True:
        count += 1
        time.sleep(1)
        print(f'Has been waiting for {count} second(s)...')


t = Thread(target=show_timer, daemon=True)
t.start()

answer = input('Do you want to exit?\n')
```

If we run the program, input something, and hit enter, the program will terminate.

```title="Output"
Do you want to exit?
Has been waiting for 1 second(s)...
Y
```

The program terminates because it doesn't need to wait for the daemon thread to complete. Also, the daemon thread is killed automatically when the program exits.

## Daemon threads vs. non-daemon threads

The following table illustrates the difference between daemon and non-daemon threads:

|                                          | Daemon Threads                      | Non-daemon Threads     |
|------------------------------------------|-------------------------------------|------------------------|
| Thread creation                          | `t = Thread(target=f, daemon=True)` | `t = Thread(target=f)` |
| The program needs to wait before exiting | :no_entry: No                       | :white_check_mark: Yes |
| Kind of tasks                            | Not critical like logging           | Critical               |