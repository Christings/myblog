---
title: python并发-11.任务调度scheduler
date: 2020-02-26 15:03:06
tags: python并发
categories:
        - python
        - python并发
---

Timer 定时器的弊端：只能控制线程在指定时间内执行一次任务，如果想实现每隔一段时间就执行一次，需要借助循环结构。

Python中sched模块，该模块中含有一个 scheduler 类，可用来执行更复杂的任务调度。

scheduler 类常用的构造方法如下：

    scheduler(timefunc=time.monotonic, delayfunc=time.sleep)

该构造方法中 2 个参数（当然也可以不提供，因为都有默认值），分别表示的含义如下：

    timefunc：指定生成时间戳的函数，默认使用 time.monotonic 来生成时间戳；
    delayfunc：在未到达指定时间前，通过该参数可以指定阻塞任务执行的函数，默认采用 time.sleep() 函数来阻塞程序。

scheduler 类的常用方法：

1、scheduler.enter(delay, priority, action, argument=(), kwargs={})
    
在 time 规定的时间后，执行 action 参数指定的函数，其中 argument 和 kwargs 负责为 action 指定的函数传参，priority 参数执行要执行任务的等级，当同一时间点有多个任务需要执行时，等级越高（ priority 值越小）的任务会优先执行。该函数会返回一个 event，可用来取消该任务。

2、scheduler.cancel(event)

取消 event 任务。注意，如果 event 参数执行的任务不存在，则会引发 ValueError 错误。

3、scheduler.run(blocking=True)

运行所有需要调度的任务。如果调用该方法的 blocking 参数为 True，该方法将会阻塞线程，直到所有被调度的任务都执行完成。

```
import threading
from sched import scheduler


def action(arg):
    print(arg)


def threadAction(*add):
    '''
    定义线程要调用的方法，*add可接收多个以非关键字方式传入的参数
    :param add:
    :return:
    '''
    sche = scheduler()  # 创建任务调度对象
    i = 3  # 定义优先级
    for arc in add:
        sche.enter(1, i, action, argument=(arc,))  # 指定1秒后执行action函数
        i -= 1
    sche.run()  # 执行所有调度的任务


myTuple = (
    'python', 'shell', 'java'
)
thread = threading.Thread(target=threadAction, args=myTuple)
thread.start()

```

注意：以上输出结果是在执行程序 1 秒后逐个输出的。    

程序创建了 thread 子线程去执行 thread_action() 函数，在该函数中使用 scheduler 类调度了 3 个任务，这 3 个任务都指定的是 1 秒后执行，其优先级分别为 3、2、1。

由于是在同一时间执行这 3 个任务，因此优先级的设定决定了谁先执行、谁后执行。显然优先级为 1 的任务优先执行，优先级为 3 的最后执行。因此上面程序执行结果中字符串的输出顺序恰好和 my_tuple 元组中的顺序是相反的？？？
