# Python Thread-safe Queue

!!! example

    ```py
    from threading import Thread
    from queue import Queue, Empty
    import time


    def producer(queue):
        for i in range(10):
            print("New task", i)
            queue.put(i)  #(2)
            time.sleep(1)


    def consumer(queue):
        while True:
            try:
                item = queue.get()  #(3)
            except Empty:
                continue
            else:
                print("Processing task", item)
                time.sleep(2)
                queue.task_done()  #(4)


    if __name__ == "__main__":
        queue = Queue()  #(1)

        # Create a producer thread and start it
        producer_thread = Thread(
            target=producer,
            args=(queue,),
        )
        producer_thread.start()

        # Create a consumer thread and start it
        consumer_thread = Thread(
            target=consumer,
            args=(queue,),
            daemon=True,  #(6)
        )
        consumer_thread.start()

        # Wait for all tasks to be added to the queue
        producer_thread.join()

        # Wait for all tasks on the queue to be completed (5)
        queue.join()
    ```

    1.  Create a new queue.
        
        To create a queue with a size limit, we can use the `maxsize` parameter.

        !!! example

            ```py
            queue = Queue(maxsize=10)
            ```

    2.  To add an item to the queue, we use the `put()` method.

        Once the queue is full, we won't be able to add an item to it. Also, the call to the `put()` method will block until the queue has space available.

        If we don't want the `put()` method to block if the queue is full, set the `block` argument to `False`:

        ```py
        queue.put(item, block=False)
        ```

        In this case, the `put()` method will raise the `queue.Full` exception if the queue is full.

        To add an item to a sized limited queue and block with a timeout, we can use the `timeout` parameter.

        ```py
        try:
            queue.put(item, timeout=3)
        except queue.Full as e:
            # handle exceptoin
        ```

    3.  To get an item from the queue, we can use the `get()` method.

        The `get()` method will block until an item is available for retrieval from the queue.

        To get an item from the queue without blocking, we can set the `block` parameter to `False`.

        To get an item from the queue and block it with a time limit, we can use the `get()` method with a `timeout`.

        ```py
        try:
            item = queue.get(timeout=10)
        except queue.Empty:
            # ...
        ```

    4.  An item that we add to the queue represents a unit of work or a task.

        When a thread calls the `get()` method to get the item from the queue, it may need to process it before the task is considered completed.

        Once completed, the thread may call the `task_done()` method of the queue to indicate that it has processed the task completely.

    5.  To wait for all tasks on the queue to be completed, we can call the `join()` method on the queue object.

    6. The consumer continue getting tasks from the queue until all the tasks are done.

    ```title="Output"
    New task 0
    Processing task 0
    New task 1
    Processing task 1
    New task 2
    New task 3
    Processing task 2
    New task 4
    New task 5
    Processing task 3
    New task 6
    New task 7
    Processing task 4
    New task 8
    New task 9
    Processing task 5
    Processing task 6
    Processing task 7
    Processing task 8
    Processing task 9
    ```