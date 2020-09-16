---
title: python并发-19.垃圾回收机制
date: 2020-02-27 23:49:58
tags: python并发
categories:
        - python
        - python并发
---
我们知道，目前的计算机都采用的是图灵机架构，其本质就是用一条无限长的纸带，对应今天的存储器。随后在工程学的推演中，逐渐出现了寄存器、易失性存储器（内存）以及永久性存储器（硬盘）等产品。由于不同的存储器，其速度越快，单位价格也就越昂贵，因此，妥善利用好每一寸告诉存储器的空间，永远是系统设计的一个核心。

Python 程序在运行时，需要在内存中开辟出一块空间，用于存放运行时产生的临时变量，计算完成后，再将结果输出到永久性存储器中。但是当数据量过大，或者内存空间管理不善，就很容易出现内存溢出的情况，程序可能会被操作系统终止。

而对于服务器这种用于永不中断的系统来说，内存管理就显得更为重要了，不然很容易引发内存泄漏。
这里的内存泄漏是指程序本身没有设计好，导致程序未能释放已不再使用的内存，或者直接失去了对某段内存的控制，造成了内存的浪费。

那么，对于不会再用到的内存空间，Python 是通过什么机制来管理的呢？就是引用计数机制。

## 一、Python引用计数机制
Python 中一切皆对象，也就是说，在 Python 中你用到的一切变量，本质上都是类对象。

那么，如何知道一个对象永远都不能再使用了呢？很简单，就是当这个对象的引用计数值为 0 时，说明这个对象永不再用，自然它就变成了垃圾，需要被回收。

```
import os
import psutil # 获取系统信息


def showMemoryInfo(hint):
    pid = os.getpid()
    p = psutil.Process(pid)

    info = p.memory_full_info()
    memory = info.uss / 1024. / 1024
    print('{} memory used: {} MB'.format(hint, memory))


def func():
    showMemoryInfo('initial')
    a = [i for i in range(10000000)]
    showMemoryInfo('after a created')


func()
showMemoryInfo('finished')
```

输出：
    
    initial memory used: 6.3828125 MB
    after a created memory used: 392.92578125 MB
    finished memory used: 10.85546875 MB

可以看到，当调用函数 func() 且列表 a 被创建之后，内存占用迅速增加到了 392 MB，而在函数调用结束后，内存则返回正常。这是因为，函数内部声明的列表 a 是局部变量，在函数返回后，局部变量的引用会注销掉，此时列表 a 所指代对象的引用计数为 0，Python 便会执行垃圾回收，因此之前占用的大量内存就又回来了。

```
import os
import psutil


def showMemoryInfo(hint):
    pid = os.getpid()
    p = psutil.Process(pid)

    info = p.memory_full_info()
    memory = info.uss / 1024. / 1024
    print('{} memory used: {} MB'.format(hint, memory))


def func():
    showMemoryInfo('initial')
    global a
    a = [i for i in range(10000000)]
    showMemoryInfo('after a created')


func()
showMemoryInfo('finished')

输出：
initial memory used: 6.37890625 MB
after a created memory used: 392.74609375 MB
finished memory used: 392.74609375 MB
```

global a 表示将 a 声明为全局变量，则即使函数返回后，列表的引用依然存在，于是 a 对象就不会被当做垃圾回收掉，依然占用大量内存。

```
import os
import psutil


def showMemoryInfo(hint):
    pid = os.getpid()
    p = psutil.Process(pid)

    info = p.memory_full_info()
    memory = info.uss / 1024. / 1024
    print('{} memory used: {} MB'.format(hint, memory))


def func():
    showMemoryInfo('initial')
    a = [i for i in range(10000000)]
    showMemoryInfo('after a created')
    return a

a = func()
showMemoryInfo('finished')

输出：
initial memory used: 6.4140625 MB
after a created memory used: 392.86328125 MB
finished memory used: 392.86328125 MB

```

如果把生成的列表返回，然后在主程序中接收，那么引用依然存在，垃圾回收也不会被触发，大量内存仍然被占用着.

??Python 内部的引用计数机制:

```
import sys

a = []
print(sys.getrefcount(a))  # 两次引用，一次来自 a，一次来自 getrefcount


def func(a):
    # 四次引用，a，python 的函数调用栈，函数参数，和 getrefcount
    print(sys.getrefcount(a))


func(a)
# 两次引用，一次来自 a，一次来自 getrefcount，函数 func 调用已经不存在
print(sys.getrefcount(a))

```
输出：

    2
    4
    2

注意：sys.getrefcount() 函数用于查看一个变量的引用次数，不过别忘了，getrefcount 本身也会引入一次计数。

注意：在函数调用发生的时候，会产生额外的两次引用，一次来自函数栈，另一个是函数参数。

```
import sys
a = []
print(sys.getrefcount(a)) # 两次
b = a
print(sys.getrefcount(a)) # 三次
c = b
d = b
e = c
f = e
g = d
print(sys.getrefcount(a)) # 八次

输出：
2
3
8
```

分析：

a、b、c、d、e、f、g 这些变量全部指代的是同一个对象，而 sys.getrefcount() 函数并不是统计一个指针，而是要统计一个对象被引用的次数，所以最后一共会有 8 次引用。

理解引用这个概念后，引用释放是一种非常自然和清晰的思想。相比 C 语言中需要使用 free 去手动释放内存，Python 的垃圾回收在这里可以说是省心省力了。

不过，有读者还是会好奇，如果想手动释放内存，应该怎么做呢？方法同样很简单，只需要先调用 del a 来删除一个对象，然后强制调用 gc.collect() 即可手动启动垃圾回收。例如：

```
import psutil
import os
import gc

def show_memory_info(hint):
    '''
    显示当前 python 程序占用的内存大小
    :param hint:
    :return:
    '''
    pid = os.getpid()
    p = psutil.Process(pid)

    info = p.memory_full_info()
    memory = info.uss / 1024. / 1024
    print('{} memory used: {} MB'.format(hint, memory))

show_memory_info('initial')
a=[i for i in range(10000000)]
show_memory_info('after a created')
del a
gc.collect()
show_memory_info('finished')
print(a)

输出：
initial memory used: 6.3984375 MB
after a created memory used: 392.77734375 MB
finished memory used: 10.70703125 MB
Traceback (most recent call last):
  File "/Users/apple/PycharmProjects/python-learning/test/Account.py", line 24, in <module>
    print(a)
NameError: name 'a' is not defined
```

问题：引用次数为 0 是垃圾回收启动的充要条件吗？还有没有其他可能性呢？

引用计数是其中最简单的实现，引用计数并非充要条件，它只能算作充分非必要条件，至于其他的可能性，下面所讲的循环引用正是其中一种。

## 二、循环引用
问题：如果有两个对象，之间互相引用，且不再被别的对象所引用，那么它们应该被垃圾回收吗？

```
import psutil
import os
import gc


def show_memory_info(hint):
    '''
    显示当前 python 程序占用的内存大小
    :param hint:
    :return:
    '''
    pid = os.getpid()
    p = psutil.Process(pid)

    info = p.memory_full_info()
    memory = info.uss / 1024. / 1024
    print('{} memory used: {} MB'.format(hint, memory))


def func():
    show_memory_info('initial')
    a = [i for i in range(10000000)]
    b = [i for i in range(10000000)]
    show_memory_info('after a,b created')
    a.append(b)
    b.append(a)


func()
show_memory_info('finished')

输出：
initial memory used: 6.4375 MB
after a,b created memory used: 779.69140625 MB
finished memory used: 779.69140625 MB
```

程序中，a 和 b 互相引用，并且作为局部变量在函数 func 调用结束后，a 和 b 这两个指针从程序意义上已经不存在，但从输出结果中看到，依然有内存占用，这是为什么呢？因为互相引用导致它们的引用数都不为 0。

试想一下，如果这段代码出现在生产环境中，哪怕 a 和 b 一开始占用的空间不是很大，但经过长时间运行后，Python 所占用的内存一定会变得越来越大，最终撑爆服务器，后果不堪设想。

有读者可能会说，互相引用还是很容易被发现的呀，问题不大。可是，更隐蔽的情况是出现一个引用环，在工程代码比较复杂的情况下，引用环真不一定能被轻易发现。那么应该怎么做呢？

Python 本身能够处理这种情况，可以通过显式调用 gc.collect() 来启动垃圾回收，例如：

```
import psutil
import os
import gc


def show_memory_info(hint):
    '''
    显示当前 python 程序占用的内存大小
    :param hint:
    :return:
    '''
    pid = os.getpid()
    p = psutil.Process(pid)

    info = p.memory_full_info()
    memory = info.uss / 1024. / 1024
    print('{} memory used: {} MB'.format(hint, memory))


def func():
    show_memory_info('initial')
    a = [i for i in range(10000000)]
    b = [i for i in range(10000000)]
    show_memory_info('after a,b created')
    a.append(b)
    b.append(a)


func()
gc.collect()
show_memory_info('finished')

输出：
initial memory used: 6.41015625 MB
after a,b created memory used: 779.81640625 MB
finished memory used: 11.4375 MB
```

事实上，Python 使用标记清除（mark-sweep）算法和分代收集（generational），来启用针对循环引用的自动垃圾回收。

1、标记清除算法：

先用图论来理解不可达的概念。对于一个有向图，如果从一个节点出发进行遍历，并标记其经过的所有节点；那么，在遍历结束后，所有没有被标记的节点，我们就称之为不可达节点。显而易见，这些节点的存在是没有任何意义的，自然的，我们就需要对它们进行垃圾回收。

当然，每次都遍历全图，对于 Python 而言是一种巨大的性能浪费。所以，在 Python 的垃圾回收实现中，标记清除算法使用双向链表维护了一个数据结构，并且只考虑容器类的对象（只有容器类对象才有可能产生循环引用）。

2、分代收集算法：

将 Python 中的所有对象分为三代。刚刚创立的对象是第 0 代；经过一次垃圾回收后，依然存在的对象，便会依次从上一代挪到下一代。而每一代启动自动垃圾回收的阈值，则是可以单独指定的。当垃圾回收器中新增对象减去删除对象达到相应的阈值时，就会对这一代对象启动垃圾回收。

事实上，分代收集基于的思想是，新生的对象更有可能被垃圾回收，而存活更久的对象也有更高的概率继续存活。因此，通过这种做法，可以节约不少计算量，从而提高 Python 的性能。
