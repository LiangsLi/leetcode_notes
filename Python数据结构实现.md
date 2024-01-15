> [TimeComplexity - Python Wiki](https://wiki.python.org/moin/TimeComplexity)
>
> [python - Complexity of len() with regard to sets and lists - Stack Overflow](https://stackoverflow.com/questions/32248882/complexity-of-len-with-regard-to-sets-and-lists)
>
> [Time Complexity of built-in len() function in Python - The Coding Bot](https://thecodingbot.com/time-complexity-of-built-in-len-function-in-python/)
>
> [Time and Space complexity analysis of Python's list.reverse() method - The Coding Bot](https://thecodingbot.com/time-and-space-complexity-analysis-of-pythons-list-reverse-method/)
>
> [python operations time complexity – JOOS](https://joosjuliet.github.io/complexity_of_python_operations/)

### List

The Average Case assumes parameters generated uniformly at random.

Internally, a `list` is represented as an `array`; the largest costs come from growing beyond the current allocation size (because everything must move), or from inserting or deleting somewhere near the beginning (because everything after that must move). If you need to add/remove at both ends, consider using a collections.deque instead.

| **Operation**                                                | **Average Case** | **[Amortized Worst Case](http://en.wikipedia.org/wiki/Amortized_analysis)** |
| ------------------------------------------------------------ | ---------------- | ------------------------------------------------------------ |
| Copy                                                         | O(n)             | O(n)                                                         |
| Append[1]                                                    | O(1)             | O(1)                                                         |
| Pop last                                                     | O(1)             | O(1)                                                         |
| Pop intermediate[2]                                          | O(n)             | O(n)                                                         |
| **Insert**                                                   | O(n)             | O(n)                                                         |
| Get Item                                                     | O(1)             | O(1)                                                         |
| Set Item                                                     | O(1)             | O(1)                                                         |
| Delete Item                                                  | O(n)             | O(n)                                                         |
| Iteration                                                    | O(n)             | O(n)                                                         |
| Get Slice                                                    | O(k)             | O(k)                                                         |
| Del Slice                                                    | O(n)             | O(n)                                                         |
| Set Slice                                                    | O(k+n)           | O(k+n)                                                       |
| Extend[1]                                                    | O(k)             | O(k)                                                         |
| [Sort](http://svn.python.org/projects/python/trunk/Objects/listsort.txt) | O(n log n)       | O(n log n)                                                   |
| Multiply                                                     | O(nk)            | O(nk)                                                        |
| **x in s**                                                   | O(n)             |                                                              |
| min(s), max(s)                                               | O(n)             |                                                              |
| Get Length                                                   | O(1)             | O(1)                                                         |

+ [Python list implementation – Laurent Luce's Blog](http://www.laurentluce.com/posts/python-list-implementation/)

+ [arrays - How is Python's List Implemented? - Stack Overflow](https://stackoverflow.com/questions/3917574/how-is-pythons-list-implemented)

### Dict

The Average Case times listed for dict objects assume that the hash function for the objects is sufficiently robust to make collisions uncommon. The Average Case assumes the keys used in parameters are selected uniformly at random from the set of all keys.

Note that there is a fast-path for dicts that (in practice) only deal with str keys; this doesn't affect the algorithmic complexity, but it can significantly affect the constant factors: how quickly a typical program finishes.

| **Operation** | **Average Case** | **Amortized Worst Case** |
| ------------- | ---------------- | ------------------------ |
| **k in d**    | O(1)             | O(n)                     |
| Copy[3]       | O(n)             | O(n)                     |
| Get Item      | O(1)             | O(n)                     |
| Set Item[1]   | O(1)             | O(n)                     |
| Delete Item   | O(1)             | O(n)                     |
| Iteration[3]  | O(n)             | O(n)                     |

+ [data structures - How are Python's Built In Dictionaries Implemented? - Stack Overflow](https://stackoverflow.com/questions/327311/how-are-pythons-built-in-dictionaries-implemented)

### Deque

+ [Python deque implementation – Laurent Luce's Blog](http://www.laurentluce.com/posts/python-deque-implementation/)
+ [How are deques in Python implemented, and when are they worse than lists? - Stack Overflow](https://stackoverflow.com/questions/6256983/how-are-deques-in-python-implemented-and-when-are-they-worse-than-lists)

### Set

See `dict` -- the implementation is intentionally very similar.

| **Operation**                     | **Average case**                                             | **Worst Case**                                | **notes**                                  |
| --------------------------------- | ------------------------------------------------------------ | --------------------------------------------- | ------------------------------------------ |
| **x in s**                        | O(1)                                                         | O(n)                                          |                                            |
| Union s\|t                        | [O(len(s)+len(t))](https://wiki.python.org/moin/TimeComplexity_(SetCode)) |                                               |                                            |
| Intersection s&t                  | O(min(len(s), len(t)))                                       | O(len(s) * len(t))                            | replace "min" with "max" if t is not a set |
| Multiple intersection s1&s2&..&sn |                                                              | (n-1)*O(l) where l is max(len(s1),..,len(sn)) |                                            |
| Difference s-t                    | O(len(s))                                                    |                                               |                                            |
| s.difference_update(t)            | O(len(t))                                                    |                                               |                                            |
| Symmetric Difference s^t          | O(len(s))                                                    | O(len(s) * len(t))                            |                                            |
| s.symmetric_difference_update(t)  | O(len(t))                                                    | O(len(t) * len(s))                            |                                            |

+ [python - How is set() implemented? - Stack Overflow](https://stackoverflow.com/questions/3949310/how-is-set-implemented)

### Python中的"队列"

> [简析Python中的四种队列 - 知乎](https://zhuanlan.zhihu.com/p/37093602)

#### 优先队列 - 最小堆: `heapq`

#### 队列: `queue.Queue VS collections.deque`

`queue.Queue` 是为了线程间通讯而设计的一个数据结构, 其目的不是通用的队列容器, 因此不支持`in`等操作, 反而有很多线程通信专用的操作(例如`put_nowait()/get_nowait()/join()`), 如果需要进程间通讯, 则应该选择`queue.Queue`;

`collections.deque`是设计用作通用队列容器的数据结构, `deque`是`双端队列(double-ended queue)`的缩写, 因此如果需要通用的`队列`或者`双端队列`数据结构时, 应该选择使用`collections.deque`, 其能获取**更好的性能**和易用性.

##### 两者用于线程间通信时的差异

`collections.deque` 和 `queue.Queue` 都可以用作线程之间的通信，但是它们在这个上下文中的区别主要在于它们的同步机制和阻塞行为：

+ `collections.deque` 是一个快速、非阻塞、独立于线程的数据结构，适合于在不需要任何锁或并发控制的情况下使用。在使用 `collections.deque` 时，您需要自己实现必要的锁和同步机制以避免并发问题。你需要使用 Lock 和判断 deque 是否为空来正确的使用了 `collections.deque`。
+ `queue.Queue` 对象则是一个同步的，阻塞的队列，适合于平滑地处理在生产者和消费者之间的数据流并控制并发。如果一个 Queue 是空的，`Queue.get()` 将会阻塞，直到有一个元素可用。同样，如果一个 Queue 已满，`Queue.put()` 将会阻塞，直到有空间可用。你不需要在应用中实现任何锁或同步控制。

因此，从最适合你的需求的角度来讲：

+ 如果你想要一个非阻塞的解决方案，并且愿意自己控制并发，那么 `collections.deque` 可能是最好的选择。
+ 而如果你想要一个自动管理并发的阻塞队列，并且需要平滑地在多个生产者消费者之间传送数据，那么 `queue.Queue` 可能是更好的选择。
