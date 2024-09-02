# Python `asyncio.create_task()`

## Simulating a long-running operation

To simulate a long-running operation, we can use the `sleep()` coroutine of the `asyncio` package:

```py
await asyncio.sleep(seconds)
```

Because `sleep()` is a coroutine, we need to use the `await` keyword.

!!! example

    The following uses the `sleep()` coroutine to simulate an API call:

    ```py
    import asyncio
    import time


    async def call_api(message, result=1000, delay=3):
        print(message)
        await asyncio.sleep(delay)
        return result


    async def main():
        start = time.perf_counter()  # Start a timer

        price = await call_api('Get stock price of GOOG...', 300)
        print(price)

        price = await call_api('Get stock price of APPL...', 400)
        print(price)

        end = time.perf_counter()
        print(f'It took {round(end-start,0)} second(s) to complete.')

    asyncio.run(main())
    ```

    Because each `call_api()` takes three seconds, and calling it twice takes six seconds.

    ```title="Output"
    Get stock price of GOOG...
    300
    Get stock price of APPL...
    400
    It took 6.0 second(s) to complete.
    ```

    In this example, we call a coroutine directly and don't put it on the event loop to run. Instead, we get a coroutine object and use the `await` keyword to execute it and get a result.

    The following picture illustrates the execution flow of the program:

    ![](https://www.pythontutorial.net/wp-content/uploads/2022/07/python-create_task-async.svg)

    In other words, we use `async` and `await` to write asynchronous code but can't run it concurrently.

To run multiple operations concurrently, we'll need to use something called tasks.

## Introduction to Python tasks

A tasks is a wrapper of a coroutine that schedules the coroutine to run on the event loop as soon as possible.

To create a task, we pass a coroutine to the `create_task()` function of the `asyncio` package. The `create_task()` function returns a `Task` object.

!!! example

    ```py
    import asyncio
    import time


    async def call_api(message, result=1000, delay=3):
        print(message)
        await asyncio.sleep(delay)
        return result


    async def main():
        start = time.perf_counter()

        task_1 = asyncio.create_task(
            call_api('Get stock price of GOOG...', 300)
        )

        task_2 = asyncio.create_task(
            call_api('Get stock price of APPL...', 300)
        )

        price = await task_1
        print(price)

        price = await task_2
        print(price)

        end = time.perf_counter()
        print(f'It took {round(end-start,0)} second(s) to complete.')


    asyncio.run(main())
    ```

    ```title="Output"
    Get stock price of GOOG...
    Get stock price of APPL...
    300
    300
    It took 3.0 second(s) to complete.
    ```

## Running other tasks while waiting

