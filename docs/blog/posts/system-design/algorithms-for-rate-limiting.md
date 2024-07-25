---
date: 2024-01-04
authors:
    - me
categories:
    - ":flag_gb:"
    - ":computer: System Design"
---

# **Algorithms for rate limiting**

In this post, we're going to discuss what a rate limiter is and which algorithms have been used for rate limiters.

<!-- more -->

## Rate limiter overview

### What is a rate limiter?

In a network system, a rate limiter is used to control the rate of traffic sent by a client or a service.

In HTTP world, a rate limiter limits the number of client requests allowed to be sent over a specified period.

If the API request count exceeds the threshold defined by the rate limiter, all the excess calls are blocked.

??? example

    - A user can write no more than 2 posts per second
    - You can create a maximum of 100 accounts per day from the same IP address
    - You can claim rewards no more than 5 times per week from the same device

### Benefits of using a rate limiter

![](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F61f39594-5594-484d-be9e-c2cb4d3973d9_1600x1050.png)

## Common rate limiting algorithms

This section will only provide a high-level understanding for popular algorithms for rate limiting.[^1]

### Fixed window counter 

The fixed window counter algorithm divides the timeline into fixed-size time windows, and assigns a counter for each window.

Each request increments the counter by some value based on the relative cost of the request.

Once the counter reaches the threshold, subsequent requests are blocked until the new time window begins.

??? example

    In this example, the time unit is 1 second and the system allows a maximum of 3 requests per second.

    In each 1-second time window, if more than 3 requests are received, subsequent requests arriving within the same time window are dropped.

    ![](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F3dd68970-87d8-4136-a132-2575a5eb42f5_1600x798.png)

This algorithm is simple to implement. A major problem with it is that a burst of traffic at the beginning or end of time windows could allow excess requests over the threshold to go through.

??? example

    The rate limiter allows a maximum of 5 requests per minute, and the available quota resets at the top of each minute.

    There are five requests between 2:00:00 and 2:01:00 and five more requests between 2:01:00 and 2:02:00. For the 1-minute window between 2:00:30 and 2:01:30, 10 requests go through. That is twice as many as the allowed number of requests.

    ![](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F6ba8ed76-d448-4312-ad84-95eebb771f1a_1600x798.png)

### Sliding window log

The sliding window log algorithm keeps track of the timestamps of individual requests in a log. The log is usually kept in a cache, such as sorted sets in Redis.

When a new request arrives, the log is checked for requests within the window. The check removes all outdated timestamps older than the start of the current time window from the logs and adds the new request to the log. The new request is allowed if the number of existing requests within the window is below the limit.

??? example

    The rate limiter allows 2 requests per minute.

    ![](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F894aa6f7-1b1a-40fc-bd6b-d75c108205de_1600x1072.png)

    1. The log is empty when a new request arrives at 1:00:01. The request is allowed.
    2. A new request arrives at 1:00:30, the timestamp is inserted into the log. Now the log size is 2. It is below the threshold of 2. This request is also allowed.
    3. Another request arrives at 1:00:50, and the timestamp is inserted into the log. The new log size is 3, which is larger than the threshold. This request is rejected.
    4. The final request arrives at 1:01:40. Requests in the range $[$1:00:40, 1:01:40$)$ are in the latest time window, but requests sent before 1:00:40 are outdated. Two outdated timestamps, 1:00:01 and 1:00:30, are removed from the log. After clearing the old log entries, the log size is 2, and the request is accepted.

This algorithm ensures that in any rolling window, requests will not exceed the limit.

However, the algorithm consumes a lot of memory. It maintains a log of timestamps, even for requests that are rejected.

### Sliding window counter

The sliding window counter algorithm is a more efficient variation of the sliding window log algorithm. It is a hybrid that combines the fixed window counter and sliding window log.

Instead of maintaining a log of request timestamps, it calculates the weighted counter for the previous time window. When a new request arrives, the counter is adjusted based on the weight, and the request is allowed if the total is below the limit.

The number of requests in the rolling window is calculated using the following formula:

$$ \tag{1}
\begin{array}{c}
\small\text{requests in} \\ \small\text{current window}
\end{array}
~ + ~ 
\begin{array}{c}
\small\text{requests in the} \\ \small\text{previous window}
\end{array}
~ \times ~
\begin{array}{c}
\small\text{overlap percentage of the} \\ \small\text{rolling window and previous window}
\end{array}
$$

??? example

    The rate limiter allows a maximum of 7 requests per minute. There are 5 requests in the previous minute and 3 in the current minute. For a new request that arrives at 30% (18 seconds) into the current minute, 

    Using formula $(1)$, we get $3 + 5 \times 0.7 = 6.5$ requests. Depending on the use case, the number can either be rounded up or down. In this example, it is rounded down to 6.

    ![](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F4c21343a-94f7-409d-b024-fb4b3a6e1ce3_1376x734.png)

    Since the rate limiter allows 7 requests per minute, the current request can go through.

!!! note
    There is another way to implement this algorithm that is more complicated. Instead of computing a weighted counter for the previous window, it uses a counter for each time slot within the window. We will not discuss this other implementation here.

This algorithm reduces storage and processing requirements compared to the sliding window log algorithm.

However, it may still allow bursts of requests to slip through. It is an approximation of the actual rate because it assumes requests in the previous window are evenly distributed.

### Token bucket

The token bucket algorithm is widely used for rate limiting. It is simple, well understood, and commonly used by large tech companies.

The token bucket algorithm uses a "bucket" to hold a token. The tokens represent the allowed number of requests. The bucket is initially filled with tokens, and tokens are added at a fixed rate over time.

When a request arrives, it consumes a token from the bucket, and the request is allowed if there are enough tokens.

??? example

    Here, the token capacity is 4. The refiller puts 2 tokens into the bucket every second. Once the bucket is full, extra tokens will overflow.

    ![](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fed30d402-1533-4637-bbb7-27bdfda16f04_1600x798.png)

    Each request consumes one token. When a request arrives, we check if there are enough tokens in the bucket. If there are enough tokens, we take one token out for each request, and the request goes through. If there are not enough tokens, the request is dropped.

    ![](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F11d02ea3-7ff9-4420-adde-0015b236780b_1600x1435.png)

    The following diagram illustrates how token consumption, refill, and rate limiting logic work. In this example, the token bucket size is 4, and the refill is 4 per 1 minute.

    ![](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe7f2f9ef-2b2b-4d0a-803a-519f9d438750_1355x1600.png)

The token bucket algorithm takes two parameters:

- Bucket size: The maximum number of tokens allowed in the bucket.
- Refill rate: number of tokens put into the bucket every second.

How many buckets do we need? This depends on the rate-limiting rules.

!!! example
    - It is usually necessary to have different buckets for different API endpoints. For instance, if a user is allowed to make 1 post per second, add 150 friends per day, and like 5 posts per second, 3 buckets are required for each user.

    - If we need to throttle requests based on IP addresses, each IP address requires a bucket.

    - If the system allows a maximum of 10000 requests per second, it makes sense to have a global bucket shared by all requests.

This algorithm allows a smooth distribution of requests and can handle burst of requests up to the bucket's capacity. It is memory efficient and relatively easy to implement.

### Leaky bucket

The leaky bucket algorithm uses a "bucket" metaphor but processes requests differently.

Requests enter the bucket and are processed at a fixed rate, simulating a "leak" in the bucket. If the bucket becomes full, new requests are discarded until there is space available.

It is usually implemented with a FIFO queue. The algorithm works as follows:

- When a request arrives, the system checks if the queue is full. If it is not full, the request is added to the queue.
- Otherwise, the request is dropped.
- Requests are pulled from the queue and processed at regular intervals.

![](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F3f8b157b-dcf2-4bd3-aee5-bd42cb8d62e9_1600x655.png)

The leaky bucket algorithm takes the following two parameters:

- Bucket size: It is equal to the queue size. The queue holds the requests to be processed at a fixed rate.
- Outflow rate: It defines how many requests can be processed at a fixed rate, usually in requests per second.

This algorithm is memory efficient given the limited queue size. Requests are processed at a fixed rate. It smooths out request bursts and enforces a consistent rate of processing. It is suitable for use cases where a stable outflow rate is required.

However, a burst of requests would fill up the queue with old requests, and if they are not processed in time, recent requests will be rate limited. It may result in longer waiting times for requests during high-traffic periods.

## Summary

Five common rate limiting algorithms:

- **Fixed Window Counter:** Divides time into fixed windows and counts the number of requests within each window.
- **Sliding Window Log:** Keeps a log of request timestamps and continuously updates it to track requests within a sliding time window.
- **Sliding Window Counter:** Combines fixed and sliding window approaches by calculating a weighted counter from previous and current windows to regulate request rates.
- **Token Bucket:** Maintains a bucket of tokens, where each token allows a request. Tokens are added at a fixed rate, and requests are processed if enough tokens are available.
- **Leaky Bucket:** Processes requests at a constant rate by adding them to a queue and handling them one-by-one, simulating a fixed outflow rate.

Each of these rate limiting algorithms has its strengths and weaknesses. The choice of the appropriate algorithm depends on the specific requirements of the system and its desired behavior under various conditions.

<!-- end -->

[^1]: [ByteByteGo, _Rate Limiting Fundamentals_](https://blog.bytebytego.com/p/rate-limiting-fundamentals)