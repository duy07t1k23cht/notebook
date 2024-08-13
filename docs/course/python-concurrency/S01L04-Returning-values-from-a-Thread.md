# How to Return Values from a Thread

Sometimes, we want to get the return values from the other threads from the main thread.

To return a value from a thread, we extend the `Thread` class and store that value in the instance of the class.

The following example illustrates how to check a specified URL and return its HTTP status code in a separate thread.

```py
from threading import Thread
import urllib.request


class HttpRequestThread(Thread):
    def __init__(self, url: str) -> None:
        super().__init__()
        self.url = url
        self.http_status_code = None
        self.reason = None

    def run(self) -> None:
        try:
            # Get the HTTP status code of the specified URL
            response = urllib.request.urlopen(self.url)
            # Assigns it to `http_status_code` field.
            self.http_status_code = response.code
        except urllib.error.HTTPError as e:
            self.http_status_code = e.code
        except urllib.error.URLError as e:
            self.reason = e.reason


def main() -> None:
    urls = [
        'https://httpstat.us/200',
        'https://httpstat.us/400'
    ]

    # create new threads
    threads = [HttpRequestThread(url) for url in urls]

    # start the threads
    [t.start() for t in threads]

    # wait for the threads to complete
    [t.join() for t in threads]

    # display the URLs with HTTP status codes
    [print(f'{t.url}: {t.http_status_code}') for t in threads]


if __name__ == '__main__':
    main()
```

```title="Output"
https://httpstat.us/200: 200
https://httpstat.us/400: 400
```

!!! abstract "Summary"
    Extend the `Thread` class and set the instance variables inside the subclass to return the values from a child thread to the main thread.