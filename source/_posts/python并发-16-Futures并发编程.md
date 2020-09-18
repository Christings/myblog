---
title: python并发-16.Futures并发编程
date: 2020-02-27 22:25:19
tags: Python并发
categories:
        - Python
        - Python并发
---
下载网站内容并打印（单线程）：
```
import requests
import time


def downloadOne(url):
    resp = requests.get(url)
    print('Read {} from {}'.format(len(resp.content), url))


def downloadAll(urls):
    for each in urls:
        downloadOne(each)


def main():
    urls = [
        'http://c.biancheng.net',
        'http://c.biancheng.net/c',
        'http://c.biancheng.net/python'
    ]
    start = time.perf_counter()
    downloadAll(urls)
    end = time.perf_counter()
    print('Download {} urls in {} seconds'.format(len(urls), end - start))


if __name__ == '__main__':
    main()
```

输出：

    Read 51828 from http://c.biancheng.net
    Read 31939 from http://c.biancheng.net/c
    Read 31996 from http://c.biancheng.net/python
    Download 3 urls in 0.5172422020696104 seconds

步骤：

    遍历存储网站的列表；
    对当前网站执行下载操作；
    等到当前操作完成后，再对下一个网站进行同样的操作，一直到结束。
    
    总共耗时约 0.51s。

单线程的优点是简单明了，但是明显效率低下，因为上述程序的绝大多数时间都浪费在了 I/O 等待上。程序每次对一个网站执行下载操作，都必须等到前一个网站下载完成后才能开始。如果放在实际生产环境中，我们需要下载的网站数量至少是以万为单位的，不难想象，这种方案根本行不通。

下载网站内容并打印（多线程）：

```
import concurrent.futures
import requests
import threading
import time


def downloadOne(url):
    resp = requests.get(url)
    print('Read {} from {}'.format(len(resp.content), url))


def downloadAll(urls):
    with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
        executor.map(downloadOne, urls)


def main():
    urls = [
        'http://c.biancheng.net',
        'http://c.biancheng.net/c',
        'http://c.biancheng.net/python'
    ]
    start = time.perf_counter()
    downloadAll(urls)
    end = time.perf_counter()
    print('Download {} urls in {} seconds'.format(len(urls), end - start))


if __name__ == '__main__':
    main()
```

输出：

    Read 31996 from http://c.biancheng.net/python
    Read 31939 from http://c.biancheng.net/c
    Read 51828 from http://c.biancheng.net
    Download 3 urls in 0.16959443595260382 seconds

    总耗时0.2，性能提升了很多。

注意：虽然线程的数量可以自己定义，但是线程数并不是越多越好，因为线程的创建、维护和删除也会有一定的开销，所以如果设置的很大，反而可能会导致速度变慢。我们往往需要根据实际的需求做一些测试，来寻找最优的线程数量。

在 downloadOne() 中使用的 requests.get() 方法是线程安全的，在多线程的环境下也可以安全使用，不会出现条件竞争（多个线程同时竞争使用同一资源）的情况。

```
# with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
    with concurrent.futures.ProcessPoolExecutor() as executor:
```

使用并行的方式去提高程序运行效率，只需要在 downloadAll() 函数中做出下面的变化，ProcessPoolExecutor() 表示创建进程池，使用多个进程并行的执行程序。不过，这里通常省略参数 workers，因为系统会自动返回 CPU 的数量作为可以调用的进程数。

输出：

    Read 51828 from http://c.biancheng.net
    Read 31939 from http://c.biancheng.net/c
    Read 31996 from http://c.biancheng.net/python
    Download 3 urls in 0.2866901899687946 seconds

并行的方式一般用在 CPU heavy 的场景中，因为对于 I/O heavy 的操作，多数时间都会用于等待，相比于多线程，使用多进程并不会提升效率。反而很多时候，因为 CPU 数量的限制，会导致其执行效率不如多线程版本。

## 一、Futures

Python Futures 模块，位于 concurrent.futures 和 asyncio 中，它们都表示带有延迟的操作。Futures 会将处于等待状态的操作包裹起来放到队列中，这些操作的状态随时可以查询，当然它们的结果（或是异常）也能够在操作完成后被获取。

通常来说，用户不用考虑如何去创建 Futures，这些 Futures 底层都会帮我们处理好，唯一要做的只是去设定这些 Futures 的执行。比如，Futures 中的 Executor 类，当执行 executor.submit(func) 时，它便会安排里面的 func() 函数执行，并返回创建好的 future 实例，以便之后查询调用。

常用函数:

比如 Futures 中的方法 done()，表示相对应的操作是否完成，返回 True 表示完成；返回 False 表示没有完成。不过要注意的是，done() 是非阻塞的，会立即返回结果。相对应的 add_done_callback(fn)，则表示 Futures 完成后，相对应的参数函数 fn 会被通知并执行调用。

Futures 中还有一个重要的函数 result()，它表示当 future 完成后，返回其对应的结果或异常。而 as_completed(fs)，则是针对给定的 future 迭代器 fs，在其完成后返回完成后的迭代器。

```
import concurrent.futures
import requests
import time


def downloadOne(url):
    resp = requests.get(url)
    print('Read {} from {}'.format(len(resp.content), url))


def downloadAll(urls):
    with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
        # with concurrent.futures.ProcessPoolExecutor() as executor:
        todo = []
        for url in urls:
            future = executor.submit(downloadOne, url)
            todo.append(future)

        for future in concurrent.futures.as_completed(todo):
            future.result()


def main():
    urls = [
        'http://c.biancheng.net',
        'http://c.biancheng.net/c',
        'http://c.biancheng.net/python'
    ]
    start = time.perf_counter()
    downloadAll(urls)
    end = time.perf_counter()
    print('Download {} urls in {} seconds'.format(len(urls), end - start))


if __name__ == '__main__':
    main()
```

输出：

    Read 31996 from http://c.biancheng.net/python
    Read 51828 from http://c.biancheng.net
    Read 31939 from http://c.biancheng.net/c
    Download 3 urls in 0.2889432869851589 seconds

首先调用 executor.submit()，将下载每一个网站的内容都放进 future 队列 to_do 等待执行。然后是 as_completed() 函数在 future 完成后便输出结果。

注意：future 列表中每个 future 完成的顺序和它在列表中的顺序并不一定完全一致。到底哪个先完成、哪个后完成，取决于系统的调度和每个 future 的执行时间。
