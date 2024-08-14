---
draft: true
date: 2024-01-05
authors:
    - me
categories:
    - "English :flag_gb:"
    - ":computer: System Design"
---
# **Consistent Hashing**

<!-- more -->

# The rehashing problem

If you have $n$ cache servers, a common way to balance the load is to use the following hash method:

$$
\text{server index} = \text{hash}(\text{key}) ~ \% ~ N
$$

where $N$ is the size of the server pool.

??? example

    We have 4 servers and 8 string keys with their hashes:

    | key  | hash     | hash % 4 |
    |------|----------|----------|
    | key0 | 18358617 | 1        |
    | key1 | 26143584 | 0        |
    | key2 | 18131146 | 2        |
    | key3 | 35863496 | 0        |
    | key4 | 34085809 | 1        |
    | key5 | 27581703 | 3        |
    | key6 | 38164978 | 2        |
    | key7 | 22530351 | 3        |

    The following figure shows the distribution of keys:

    ![](../../images/hashing-example.png)

This approach works well when the size of the server pool is fixed, and the data distribution is even. Problems arise when new servers are added, or existing servers are removed.

??? example

    If server 1 goes offline, the size of the server pool becomes 3. We get the new table:

    | key  | hash     | hash % 4 |
    |------|----------|----------|
    | key0 | 18358617 | 0        |
    | key1 | 26143584 | 0        |
    | key2 | 18131146 | 1        |
    | key3 | 35863496 | 2        |
    | key4 | 34085809 | 1        |
    | key5 | 27581703 | 0        |
    | key6 | 38164978 | 1        |
    | key7 | 22530351 | 0        |

    and the new distribution:

    ![alt text](../../images/rehashing-example.png)

    When a server goes offline, most keys are redistrbuted, not juist the ones originally stored in the offline server. This means that most cache clients will connect to the wrong servers to fetch data. This causes a storm of cache misses.

Consistent hashing is an effective technique to mitigate this problem.

## Consistent Hashing

!!! quote "Wikipedia"

    In computer science, consistent hashing is a special kind of hashing technique such that when a hash table is resized, only $\displaystyle n/m$ keys need to be remapped on average where $n$ is the number of keys and $m$ is the number of slots. In contrast, in most traditional hash tables, a change in the number of array slots causes nearly all keys to be remapped because the mapping between the keys and the slots is defined by a modular operation.

    Consistent hashing evenly distributes cache keys across shards, even if some of the shards crash or become unavailable.

### Hash space and hash ring