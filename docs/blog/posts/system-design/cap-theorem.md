---
date: 2024-01-03
authors:
    - me
categories:
    - ":flag_gb:"
    - ":computer: System Design"
tags:
    - CAP theorem
---

# **The CAP theorem**

In this post, we're going to explore what the CAP theorem is and its relevance in today’s distributed systems.

<!-- more -->

To be more intuitive, let's use a real-life example to illustrate the concepts:[^1]

!!! example "Tiny Bank example"

    Let's say we have a tiny bank with exactly two ATMs connected over a network. The ATMs support three operations: deposit, withdraw, and check balance. There is no central database to keep the account balance, it is stored on both ATMs. No matter what happens, the balance should never go below zero.

    <figure markdown="span">
        ![Image title](../../images/cap-bank-example.png){ width="70%" }
    </figure>

## What is the CAP theorem?

![alt text](../../images/cap.png)

In database theory, the CAP theorem states that any distributed data store can provide only two of the following three guarantees:

- **Consistency**: Refers to the property of a system where all nodes have a consistent view of the data. It means all clients see the same data at the same time no matter which node they connect to.

    ??? example "Tiny Bank example"
        Consistency means that the balance is always the same on both ATMs. When a customer uses an ATM, no matter which one they are using, they always see the same balance.

- **Availability**: Refers to the ability of a system to respond to requests from users at all times.

    ??? example "Tiny Bank example"
        Availability means that clients can always perform operations at any ATM.

- **Partition tolerance**: Refers to the ability of a system to continue operating even if there is a network partition.


    ??? example "Tiny Bank example"
        Partition tolerance means that the bank should remain operational even if the two ATMs are unable to communicate with each other.
        

!!! note

    In practice, during a network partition, a system must choose between **consistency** and **availability**.
    
    If the system prioritizes **consistency**, it may become unavailable until the partition is resolved.
    
    Conversely, if the system prioritizes **availability**, it may allow updates to the data, potentially resulting in data inconsistencies until the partition is resolved.
    
    <figure markdown="span">
        ![Image title](../../images/cap-note-1.png){ width="70%" }
    </figure>
    
    We will discuss this in more detail in the next section.

## CAP theorem NoSQL database types

NoSQL databases are ideal for distributed network applications. Unlike their vertically scalable SQL (relational) counterparts, NoSQL databases are horizontally scalable and distributed by design.[^2]

Today, NoSQL databases are classified based on the two CAP characteristics they support:

### CP database

A CP database delivers **consistency** and **partition tolerance** at the expense of availability.

When a partition occurs between any two nodes, the system has to shut down the non-consistent node (i.e., make it unavailable) until the partition is resolved.

??? example "Tiny Bank example"

    If there is a network partition and the ATMs are unable to communicate with each other, and the bank prioritizes consistency, the ATMs may refuse to process deposits or withdrawals until the partition is resolved. This ensures that the balance remains consistent, but the system is unavailable to customers.

    <figure markdown="span">
        ![Image title](../../images/cap-atm-cp.png){ width="70%" }
        <figcaption>If a network partition occurs, customers cannot make deposits or withdrawals, ensuring the system's consistency.</figcaption>
    </figure>

### AP database

An AP database delivers **availability** and **partition tolerance** at the expense of consistency.

When a partition occurs, all nodes remain available but those at the wrong end of a partition might return an older version of data than others. (When the partition is resolved, the AP databases typically resync the nodes to repair all inconsistencies in the system.)

??? example "Tiny Bank example"

    If there is a network partition and the ATMs are unable to communicate with each other, and the bank prioritizes availability, the ATM may allow deposits and withdrawals to occur, but the balance may become inconsistent until the partition is resolved.

    <figure markdown="span">
        ![Image title](../../images/cap-atm-ap.png){ width="70%" }
        <figcaption>If a network partition occurs, the balance may become inconsistent when customers make deposits or withdrawals.</figcaption>
    </figure>

### CA database

A CA database delivers **consistency** and **availability** across all nodes. It can’t do this if there is a partition between any two nodes in the system. Therefore, it can’t deliver fault tolerance.

In practice, this means that for a CA database to function correctly, it must operate in an environment where network partitions are impossible or extremely rare.

??? example "Tiny Bank example"

    If the system requires both consistency and availability, then network partitions must not occur. If a network partition does happen, the system will become unavailable, users will experience downtime.
    

!!! note

    In a distributed system, partitions can’t be avoided. So, while we can discuss a CA distributed database in theory, for all practical purposes, a CA distributed database can’t exist. This doesn’t mean you can’t have a CA database for your distributed application if you need one. Many relational databases, such as PostgreSQL, deliver consistency and availability in a single-node setup or with replication, assuming no network partitions occur.[^2]

## Summary

The CAP theorem is a useful tool for understanding the high-level trade-offs to consider during a network partition. While it is a good starting point, it does not provide a complete picture of the trade-offs involved in designing a comprehensive distributed system.

Despite numerous advancements in NoSQL databases and other distributed systems, the CAP theorem remains relevant today as a fundamental tradeoff in distributed system designs.[^3]

!!! note

    The CAP theorem assumes 100% availability or 100% consistency. In the real world, there are degrees of consistency and availability that distributed system designers must carefully consider.

    ??? example "Tiny Bank Example"

        During a network partition, the ATMs could allow only balance inquiries to be processed, while deposits or withdrawals are blocked. This maintains the system's consistency but is not 100% unavailable to customers (customers can still perform balance inquiries).

        <figure markdown="span">
            ![Image title](../../images/cap-atm-cp-a.png){ width="70%" }
            <figcaption>If a network partition occurs, the ATMs could allow only balance inquiries to be processed, while deposits or withdrawals are blocked.</figcaption>
        </figure>

<!-- end -->

[^1]: [ByteByteGo, _CAP Theorem Simplified_](https://youtu.be/BHqjEjzAicA)
[^2]: [IBM, _What is the CAP theorem?_](https://www.ibm.com/topics/cap-theorem)
[^3]: [RR, _CAP theorem — Is it still relevant?_](https://medium.com/@rrinlondon/cap-theorem-is-it-still-relevant-c3a99cc3fc38)