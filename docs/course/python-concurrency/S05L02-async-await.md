# Python `async`/`await`

## Introduction to Python coroutines

A **coroutine** is a regular function with the ability to pause its execution when encoutering an operation that may take a while to complete.

When the long-running operation completes, we can resume the paused coroutine and execute the remaining code in that coroutine.

While the coroutine is waiting for the long-running operation, we can run other code. By doing this, we can run the program asynchronously to improve its performance.

To create and pause a coroutine, we use the Python `async` and `await` keywords.

## Defining and pausing a coroutine with Python `async`/`await` keyword

!!! example

    ```py
    import asyncio


    async def square(number: int) -> int:
        return number*number


    async def main() -> None:
        x = await square(10)  #(1)
        print(f'x={x}')

        y = await square(5)  #(2)
        print(f'y={y}')

        print(f'total={x+y}')  # Display the total

    if __name__ == '__main__':
        asyncio.run(main())
    ```

    1.  Call the `square()` coroutine using the `await` keyword. The `await` keyword will pause the execution of the `main()` coroutine, wait for the `square()` coroutine to complete, and return the result.

    2.  Call the `square()` coroutine a second time using the `await` keyword.

    This program executes like a synchronous program. It doesn't reveal the power of the asynchronous programming model.

!!! abstract "Summary"

    -   A coroutine is a regular function with the power of pausing a long-running operation, waiting for the result, and resuming from paused point.
    -   Use `async` keyword to define a coroutine.
    -   Use `await` keyword to pause a coroutine.
    -   Use `asyncio.run()` function to automatically execute a coroutine on an event loop and manage an event loop.