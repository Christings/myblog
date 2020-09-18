---
title: python并发-18.GIL全局解释器锁
date: 2020-02-27 23:30:32
tags: Python并发
categories:
        - Python
        - Python并发
---
GIL，中文译为全局解释器锁。

```
import time

start = time.clock()


def count(n):
    while n > 0:
        n -= 1


count(10000)
print('Time used:', (time.clock() - start))
```

输出：Time used: 0.0006330000000000016

使用多（适量）线程可以提升程序性能：
```
import time
from threading import Thread

start = time.clock()


def count(n):
    while n > 0:
        n -= 1


t1 = Thread(target=count, args=[10000 // 2])
t2 = Thread(target=count, args=[10000 // 2])
t1.start()
t2.start()
t1.join()
t2.join()
print('Time used:', (time.clock() - start))
```
输出：Time used: 0.0007749999999999979

从输出结果看，多线程不但没有提高性能，反而降低了。

    如果使用更多线程进行尝试，会发现其运行效率和 2 个线程效率几乎一样（本机器测试使用 4 个线程，其执行效率约为 0.001）。

事实上，得到这样的结果是肯定的，因为 GIL 限制了 Python 多线程的性能不会像我们预期的那样。

那么，什么是 GIL 呢？GIL 是最流程的 CPython 解释器（平常称为 Python）中的一个技术术语，中文译为全局解释器锁，其本质上类似操作系统的 Mutex。GIL 的功能是：在 CPython 解释器中执行的每一个 Python 线程，都会先锁住自己，以阻止别的线程执行。

当然，CPython 不可能容忍一个线程一直独占解释器，它会轮流执行 Python 线程。这样一来，用户看到的就是“伪”并行，即 Python 线程在交替执行，来模拟真正并行的线程。

有读者可能会问，既然 CPython 能控制线程伪并行，为什么还需要 GIL 呢？其实，这和 CPython 的底层内存管理有关。

CPython 使用引用计数来管理内容，所有 Python 脚本中创建的实例，都会配备一个引用计数，来记录有多少个指针来指向它。当实例的引用计数的值为 0 时，会自动释放其所占的内存。

```
>>> import sys
>>> a = []
>>> b = a
>>> sys.getrefcount(a)
3
```

a 的引用计数值为 3，因为有 a、b 和作为参数传递的 getrefcount 都引用了一个空列表。

假设有两个 Python 线程同时引用 a，那么双方就都会尝试操作该数据，很有可能造成引用计数的条件竞争，导致引用计数只增加 1（实际应增加 2），这造成的后果是，当第一个线程结束时，会把引用计数减少 1，此时可能已经达到释放内存的条件（引用计数为 0），当第 2 个线程再次视图访问 a 时，就无法找到有效的内存了。

所以，CPython 引进 GIL，可以最大程度上规避类似内存管理这样复杂的竞争风险问题。

## 一、GIL底层实现原理
![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/GIL.gif](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/GIL.gif)

GIL 在 Python 程序的工作流程：

Thread 1、2、3 轮流执行，每一个线程在开始执行时，都会锁住 GIL，以阻止别的线程执行；同样的，每一个线程执行完一段后，会释放 GIL，以允许别的线程开始利用资源。

为什么 Python 线程会去主动释放 GIL 呢？毕竟，如果仅仅要求 Python 线程在开始执行时锁住 GIL，且永远不去释放 GIL，那别的线程就都没有运行的机会。其实，CPython 中还有另一个机制，叫做间隔式检查（check_interval），意思是 CPython 解释器会去轮询检查线程 GIL 的锁住情况，每隔一段时间，Python 解释器就会强制当前线程去释放 GIL，这样别的线程才能有执行的机会。

注意：不同版本的 Python，其间隔式检查的实现方式并不一样。早期的 Python 是 100 个刻度（大致对应了 1000 个字节码）；而 Python 3 以后，间隔时间大致为 15 毫秒。当然，我们不必细究具体多久会强制释放 GIL，读者只需要明白，CPython 解释器会在一个“合理”的时间范围内释放 GIL 就可以了。

每一个 Python 线程都是类似这样循环的封装，来看下面这段代码：

```
for (;;) {
    if (--ticker < 0) {
        ticker = check_interval;   
        /* Give another thread a chance */
        PyThread_release_lock(interpreter_lock);
        /* Other threads may run now */   
        PyThread_acquire_lock(interpreter_lock, 1);
    }
    bytecode = *next_instr++;
    switch (bytecode) {
        /* execute the next instruction ... */
    }
}
```


每个 Python 线程都会先检查 ticker 计数。只有在 ticker 大于 0 的情况下，线程才会去执行自己的代码。

## ?二、GIL不能绝对保证线程安全
注意：有了 GIL，并不意味着 Python 程序员就不用去考虑线程安全了，因为即便 GIL 仅允许一个 Python 线程执行，但别忘了 Python 还有 check interval 这样的抢占机制。

```
import threading

n = 0

def foo():
    global n
    n += 1

threads = []
for i in range(100):
    t = threading.Thread(target=foo)
    threads.append(t)

for i in threads:
    i.start()

for i in threads:
    i.join()
print(n)

```

执行此代码会发现，其大部分时候会打印 100，但有时也会打印 99 或者 98，原因在于 n+=1 这一句代码让线程并不安全。如果去翻译 foo 这个函数的字节码就会发现，它实际上是由下面四行字节码组成：

```
>>> import dis
>>> dis.dis(foo)
LOAD_GLOBAL              0 (n)
LOAD_CONST               1 (1)
INPLACE_ADD
STORE_GLOBAL             0 (n)
```

而这四行字节码中间都是有可能被打断的！所以，千万别以为有了 GIL 程序就不会产生线程问题，我们仍然需要注意线程安全。


