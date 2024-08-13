---
date: 2024-01-02
authors:
    - me
categories:
    - ":flag_gb:"
    - ":computer: System Design"
tags:
    - Load Balancing
# slug: load-balancing-algorithms
---

# **Load Balancing Algorithms**

In this post, we'll provide a comprehensive overview of load balancing algorithms, discussing how they work and their pros and cons.

<!-- more -->

A load balancer is a device that distributes network traffic across multiple servers. A load balancing algorithm is the logic a load balancer uses to distribute network traffic among these servers.[^1]

There are two main categories of algorithms: *static* and *dynamic*. Let's explore each category and dive deeper into the major specific algorithms.[^2]

## Static Algorithms

Static load balancing algorithms distribute requests to servers without taking into account the servers' real-time conditions and performance metrics.

### Round Robin

![alt text](../../images/round-robin.png)

!!! quote "Description"
    Distributes requests evenly among servers in sequence.

!!! success "Pros"
    - Easy to implement and understand.

!!! failure "Cons"
    - Can potentially overload servers if they are not properly monitored.

### Sticky Round Robin

![alt text](../../images/sticky-round-robin.png)

!!! quote "Description"
    An extension of round robin that tries to route subsequent requests from the same user to the same server.

!!! success "Pros"
    - Improves performance by keeping related data on the same server.

!!! failure "Cons"
    - Uneven loads can easily occur since newly arriving users are assigned randomly.

### Weighted Round Robin

![alt text](../../images/weighted-round-robin.png)

!!! quote "Description"
    Allows admins to assign different weights or priorities to different servers. Servers with higher weights receive higher number of requests.

!!! success "Pros"
    - Accounts for heterogeneous server capabilities.

!!! failure "Cons"
    - Weights must be manually configured, which is less adaptive to real-time changes.

### Hash-Based Algorithms

![alt text](../../images/hash-based.png)

!!! quote "Description"
    Uses a hash function to map incoming requests to the backend servers. The hash function often uses the client's IP address or the requested URL as input for determining where to route each request.

!!! success "Pros"
    - Can evenly distribute requests if the function is chosen wisely.

!!! failure "Cons"
    - Selecting an optimal hash function can be challenging.

## Dynamic Algorithms

Dynamic load balancing algorithms adapt in real-time by taking active performance metrics and server conditions into account when distributing requests.

### Least Connections

![alt text](../../images/least-connections.png)

!!! quote "Description"
    Sends each new request to the server currently with the least number of active connections or open requests.

!!! success "Pros"
    - New requests are adaptively routed to where there is the most remaining capacity.

!!! failure "Cons"
    - Requires actively tracking the number of ongoing connections on each backend server.
    - Load can unintentionally concentrate on certain servers if connections pile up unevenly.

### Least Response Time

![alt text](../../images/least-time.png)

!!! quote "Description"
    Sends incoming requests to the server with the lowest current latency or fastest response time. Latency for each server is continuously measured and factored in.

!!! success "Pros"
    - Highly adaptive and reactive.

!!! failure "Cons"
    - Requires constant monitoring, which incurs significant overhead and introduces complexity.
    - Does not consider how many existing requests each server already has.

## Summary

### Static Algorithms

There are clear tradeoffs between simple static algorithms and more adaptive dynamic ones. Static algorithms like round robin work well for stateless applications. Dynamic algorithms help optimize response times and availability for large, complex applications.[^2]

[^1]: [Cloudflare, _Types of load balancing algorithms_](https://www.cloudflare.com/learning/performance/types-of-load-balancing-algorithms/)
[^2]: [ByteByteGo, _Top 6 Load Balancing Algorithms Every Developer Should Know_](https://youtu.be/dBmxNsS3BGE)