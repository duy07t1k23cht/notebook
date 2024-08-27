# How to Stop a Thread in Python

## Introduction to the Event object

To stop a child thread from the main thread, you use an `Event` object with the following steps:

-   First, create a new `Event` object and pass it to a child thread.
-   Second, periodically check if the internal flag of the `Event` object is set in the child thread by calling the `is_set()` method and stop the child thread if the internal flag was set.
-   Third, call the `set()` method in the main thread at some point in time to stop the child thread.

The following flow chart illustrates the above steps:

## A simple example of stopping a thread in Python

!!! example

    ```py
    from threading import Thread, Event
    from time import sleep


    def task(event: Event) -> None:
        for i in range(6):
            print(f"Running #{i+1}")
            sleep(1)
            if event.is_set():
                print("The thread was stopped prematurely.")
                break  #(1)
        else:
            print("The thread was stopped maturely.")


    def main() -> None:

        event = Event()
        thread = Thread(target=task, args=(event,))

        # start the thread
        thread.start()

        # suspend  the thread after 3 seconds
        sleep(3)  #(2)

        # stop the child thread
        event.set()


    if __name__ == "__main__":
        main()
    ```

    1.  Stop the thread whenever the event is set.
    2.  The main thread set the event after 3 seconds.

        After the event is set, the child thread will be notified and break the loop.

## Stopping a thread that uses a child class of the `Thread` class

Sometimes, we may want to extend the `Thread` class and override the `run()` method for creating a new thread:

```py
class MyThread(Thread):
    def run(self):
        pass
```

!!! example

    ```py
    from threading import Thread, Event
    from time import sleep

    class Worker(Thread):
        def __init__(self, event, *args, **kwargs):
            super().__init__(*args, **kwargs)
            self.event = event

        def run(self) -> None:
            for i in range(6):
                print(f'Running #{i+1}')
                sleep(1)
                if self.event.is_set():
                    print('The thread was stopped prematurely.')
                    break
            else:
                print('The thread was stopped maturely.')

    def main() -> None:

        # create a new Event object
        event = Event()

        # create a new Worker thread
        thread = Worker(event)
        
        # start the thread
        thread.start()

        # suspend  the thread after 3 seconds
        sleep(3)

        # stop the child thread
        event.set()    
    

    if __name__ == '__main__':
        main()
    ```

!!! abstract "Summary"

    -   Use the `Event` object to stop a child thread.
    -   Use the `set()` method to set an internal flag of an `Event` object to `True`.
    -   Use `is_set()` method to check if the internal flag of an `Event` object was set.