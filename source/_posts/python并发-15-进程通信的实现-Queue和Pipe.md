---
title: python并发-15.进程通信的实现-Queue和Pipe
date: 2020-02-27 21:59:56
tags: python并发
categories:
        - python
        - python并发
---
Python 提供了多种实现进程间通信的机制，主要有以下 2 种：

    Python multiprocessing 模块下的 Queue 类，提供了多个进程之间实现通信的诸多方法；
    Pipe，又被称为“管道”，常用于实现 2 个进程之间的通信，这 2 个进程分别位于管道的两端。

## 一、Queue实现进程间的通信
Queue 实现进程间通信的方式，就是使用了操作系统给开辟的一个队列空间，各个进程可以把数据放到该队列中，当然也可以从队列中把自己需要的信息取走。

Python multiprocessing Queue 类常用方法:

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/thread2.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/thread2.png)

```
import multiprocessing


def action(queue, name):
    print(multiprocessing.current_process().pid, '进程放数据', name)
    queue.put(name)


if __name__ == '__main__':
    queue = multiprocessing.Queue()  # 创建进程通信的Queue
    process = multiprocessing.Process(target=action, args=(queue, 'python'))  # 创建子进程
    process.start()  # 启动子进程
    process.join()  # 子进程必须先执行完毕
    print(multiprocessing.current_process().pid, '取数据：')
    print(queue.get())
```

输出：

    9680 进程放数据 python
    9677 取数据：
    python

## 二、Pipe实现进程间通信
Pipe实现多进程编程的方式，和实际生活中的管（管道）是非常类似的。
通常情况下，管道有 2 个口，而 Pipe 也常用来实现 2 个进程之间的通信，这 2 个进程分别位于管道的两端，一端用来发送数据，另一端用来接收数据。

Pipe 实现进程通信，首先需要调用 multiprocessing.Pipe() 函数来创建一个管道，语法如下：

    conn1, conn2 = multiprocessing.Pipe( [duplex=True] )

conn1 和 conn2 分别用来接收 Pipe 函数返回的 2 个端口；duplex 参数默认为 True，表示该管道是双向的，即位于 2 个端口的进程既可以发送数据，也可以接受数据，而如果将 duplex 值设为 False，则表示管道是单向的，conn1 只能用来接收数据，而 conn2 只能用来发送数据。

conn1 和 conn2 都属于 PipeConnection 对象，可调用方法如下：

![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/pipe.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/pipe.png)

```
import multiprocessing


def action(conn, name):
    print(multiprocessing.current_process().pid, '进程发送数据', name)
    conn.send(name)


if __name__ == '__main__':
    conn1, conn2 = multiprocessing.Pipe()  # 创建Pipe
    process = multiprocessing.Process(target=action, args=(conn1, 'python'))
    process.start()
    process.join()

    print(multiprocessing.current_process().pid,'接收数据：')
    print(conn2.recv())
```

输出：

    11070 进程发送数据 python
    11069 接收数据：
    python

