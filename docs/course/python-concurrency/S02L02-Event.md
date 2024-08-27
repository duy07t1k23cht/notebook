# Python Threading Event

## Introduction to the Python threading Event object

Python provides a way to communicate between threads using the `Event` class from the `threading` module. One thread signals an event while other threads wait for it.

The `Event` object wraps a boolean flag that can be set (`True`) or cleared (`False`). Multiple threads can wait for an `Event` to be set before proceeding or can reset the `Evetn` back to the cleared state.

To use the `Event` object, first, import the `Event` from the `threading` module:

```py
from threading import Event
```

Next, create a new `Event` object:

```py
event = Event()
```

By default, the event is not set (cleared). The `is_set()` method of the event object will return `False`:

```py
if event.is_set():
   # ...
```

Then, set an event using the `set()` method:

```py
event.set()
```

Once an event is set, all the threads that wait on the event will be notified automatically.

After that, unset an event via the `clear()` method:

```py
event.clear()
```

Finally, threads can wait for the event to be set via the `wait()` method:

```py
event.wait()
```

The `wait()` method blocks the execution of a thread until the event is set. In other words, the `wait()` method will block the current thread until another thread call the `set()` method to set the event.

If an event is set, the `wait()` method returns immediately.

To specify how long the thread is going to wait, we can use the `timeout` argument.

!!! example

    The following example shows a simple example of using the `Event` object to communicate between threads:

    ```py
    from threading import Thread, Event
    from time import sleep


    def task(event: Event, id: int) -> None:
        print(f"Thread {id} started. Waiting for the signal....")
        event.wait()  #(1)
        print(f"Received signal. The thread {id} was completed.")  #(2)


    def main() -> None:
        event = Event()

        t1 = Thread(target=task, args=(event, 1))
        t2 = Thread(target=task, args=(event, 2))

        t1.start()
        t2.start()

        print("Blocking the main thread for 3 seconds...")
        sleep(3)
        event.set()  #(3)


    if __name__ == "__main__":
        main()
    ```

    1.  We call the `wait()` method of the `event` object to wait for the event to be set by the main thread.
    
        The `wait()` method blocks the thread until the event is set.
    2.  After the event is set by the main thread, the `wait()` method returns immediately. The thread continue running.
    3.  The main thread set the event. Both `t1` and `t2` threads will be notified and continue executing until the end.

    ```title="Output"
    Thread 1 started. Waiting for the signal....
    Thread 2 started. Waiting for the signal....
    Blocking the main thread for 3 seconds...
    Received signal. The thread 1 was completed.
    Received signal. The thread 2 was completed.
    ```

We'll learn how to use the `Event` object to stop a child thread from the main thread in the next lecture.

## Practical example of using Threading event

!!! example

    The following example illustrates how to use the threading event to synchronize between two threads:

    -   Thread #1 downloads a text file from URL `https://www.ietf.org/rfc/rfc2821.txt`, once completed, it notifies the second thread to count the words from the downloaded text file.
    -   Thread #2 starts and wait for the completed signal from the thread #1. Once, receiving the signal, it starts counting the words from the downloaded file.

    Here's the complete program:

    ```py
    from threading import Thread, Event
    from urllib import request


    def download_file(url: str, event: Event):
        # Download the file form URL
        print(f"Downloading file from {url}...")
        filename, _ = request.urlretrieve(url, url.split("/")[-1])

        # File download completed, set the event
        event.set()


    def process_file(event: Event):
        print("Waiting for the file to be downloaded...")
        event.wait()  # Wait for the event to be set

        # File has been downloaded, start processing it
        print("File download completed. Starting file processing...")

        # Count the number of words in the file
        word_count = 0
        with open("rfc2821.txt", "r") as file:
            for line in file:
                words = line.split()
                word_count += len(words)

        # Print the word count
        print(f"Number of words in the file: {word_count}")


    def main():
        # Create an Event object
        event = Event()

        # Create and start the file download thread
        download_thread = Thread(
            target=download_file,
            args=("https://www.ietf.org/rfc/rfc2821.txt", event)
        )

        download_thread.start()

        # Create and start the file processing thread
        process_thread = Thread(target=process_file, args=(event,))
        process_thread.start()

        # Wait for both threads to complete
        download_thread.join()
        process_thread.join()

        print("Main thread finished.")


    if __name__ == "__main__":
        main()
    ```

!!! abstract "Summary"

    -   Use the `threading.Event` class to communicate between threads.
    -   Use the `set()` method to set the event and `clear()` method to unset the event.
    -   Use the `is_set()` method to check if an event is set.
    -   Use the `wait()` method to wait for the event to be set.