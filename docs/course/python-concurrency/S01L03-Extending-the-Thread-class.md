# How to Extend Python Thread Class

## Introduction to Python Thread Class

One way to execute code in a new thread is to extend the `Thread` class from the `threading` module and override the `run()` method in the subclass to customize the behavior of the new thread when it is created.

Let's take an example of extending the `Thread` class. We'll develop a class that performs an HTTP request to a URL and display the response code:

```py
from threading import Thread
import urllib.request


class HttpRequestThread(Thread):
    def __init__(self, url: str) -> None:
        super().__init__()
        self.url = url

    def run(self) -> None:
        print(f'Checking {self.url} ...')
        try:
            response = urllib.request.urlopen(self.url)
            print(response.code)
        except urllib.error.HTTPError as e:
            print(e.code)
        except urllib.error.URLError as e:
            print(e.reason)


def main() -> None:
    urls = [
        'https://httpstat.us/200',
        'https://httpstat.us/400'
    ]

    threads = [HttpRequestThread(url) for url in urls]

    [t.start() for t in threads]

    [t.join() for t in threads]


if __name__ == '__main__':
    main()
```
```title="Output"
Checking https://httpstat.us/200 ...
Checking https://httpstat.us/400 ...
200
400
```