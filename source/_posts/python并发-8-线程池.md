---
title: python并发-8.线程池
date: 2020-02-25 22:47:40
tags: python并发
categories:
        - python
        - python并发
---
系统启动一个新线程的成本是比较高的，因为它涉及与操作系统的交互。在这种情形下，使用线程池可以很好地提升性能，尤其是当程序中需要创建大量生存期很短暂的线程时，更应该考虑使用线程池。

线程池在系统启动时即创建大量空闲的线程，程序只要将一个函数提交给线程池，线程池就会启动一个空闲的线程来执行它。当该函数执行结束后，该线程并不会死亡，而是再次返回到线程池中变成空闲状态，等待执行下一个函数。

此外，使用线程池可以有效地控制系统中并发线程的数量。当系统中包含有大量的并发线程时，会导致系统性能急剧下降，甚至导致 Python 解释器崩溃，而线程池的最大线程数参数可以控制系统中并发线程的数量不超过此数。

## 一、进程池的使用
线程池基类是 concurrent.futures 模块中的 Executor
Executor 两个子类:

    ThreadPoolExecutor：用于创建线程池
    ProcessPoolExecutor：用于创建进程池

如果使用线程池/进程池来管理并发编程，那么只要将相应的 task 函数提交给线程池/进程池，剩下的事情就由线程池/进程池来搞定。

Exectuor 提供的常用方法：
    
    submit(fn, *args, **kwargs)：将 fn 函数提交给线程池。*args 代表传给 fn 函数的参数，**kwargs 代表以关键字参数的形式为 fn 函数传入参数。

    map(func, *iterables, timeout=None, chunksize=1)：该函数类似于全局函数 map(func, *iterables)，只是该函数将会启动多个线程，以异步方式立即对 iterables 执行 map 处理。

    shutdown(wait=True)：关闭线程池。

程序将 task 函数提交（submit）给线程池后，submit 方法会返回一个 Future 对象，Future 类主要用于获取线程任务函数的返回值。由于线程任务会在新线程中以异步方式执行，因此，线程执行的函数相当于一个“将来完成”的任务，所以 Python 使用 Future 来代表。

实际上，在 Java 的多线程编程中同样有 Future，此处的 Future 与 Java 的 Future 大同小异。

Future 提供了如下方法：

    cancel()：取消该 Future 代表的线程任务。如果该任务正在执行，不可取消，则该方法返回 False；否则，程序会取消该任务，并返回 True。
    cancelled()：返回 Future 代表的线程任务是否被成功取消。
    running()：如果该 Future 代表的线程任务正在执行、不可被取消，该方法返回 True。
    done()：如果该 Funture 代表的线程任务被成功取消或执行完成，则该方法返回 True。
    result(timeout=None)：获取该 Future 代表的线程任务最后返回的结果。如果 Future 代表的线程任务还未完成，该方法将会阻塞当前线程，其中 timeout 参数指定最多阻塞多少秒。
    exception(timeout=None)：获取该 Future 代表的线程任务所引发的异常。如果该任务成功完成，没有异常，则该方法返回 None。
    add_done_callback(fn)：为该 Future 代表的线程任务注册一个“回调函数”，当该任务成功完成时，程序会自动触发该 fn 函数。

在用完一个线程池后，应该调用该线程池的 shutdown() 方法，该方法将启动线程池的关闭序列。调用 shutdown() 方法后的线程池不再接收新任务，但会将以前所有的已提交任务执行完成。当线程池中的所有任务都执行完成后，该线程池中的所有线程都会死亡。

使用线程池来执行线程任务的步骤如下：

    调用 ThreadPoolExecutor 类的构造器创建一个线程池。
    定义一个普通函数作为线程任务。
    调用 ThreadPoolExecutor 对象的 submit() 方法来提交线程任务。
    当不想提交任何任务时，调用 ThreadPoolExecutor 对象的 shutdown() 方法来关闭线程池。

```
from concurrent.futures import ThreadPoolExecutor
import threading
import time


def action(max):
    '''
    准备作为线程任务的函数
    :param max:
    :return:
    '''
    sum = 0
    for i in range(max):
        print(threading.current_thread().name + ' ' + str(i))
        sum += i
    return sum


pool = ThreadPoolExecutor(max_workers=2)  # 创建包含2个线程的线程池
future1 = pool.submit(action, 50)  # 向线程池提交一个task, 50会作为action()函数的参数
future2 = pool.submit(action, 100)
print(future1.done())  # 判断future1代表的任务是否结束
time.sleep(3)
print(future2.done())  # 判断future2代表的任务是否结束
print(future1.result())  # 查看future1代表的任务返回的结果
print(future2.result())  # 查看future2代表的任务返回的结果
pool.shutdown()
```

当程序把 action() 函数提交给线程池时，submit() 方法会返回该任务所对应的 Future 对象，程序立即判断 futurel 的 done() 方法，该方法将会返回 False（表明此时该任务还未完成）。接下来主程序暂停 3 秒，然后判断 future2 的 done() 方法，如果此时该任务已经完成，那么该方法将会返回 True。

当程序使用 Future 的 result() 方法来获取结果时，该方法会阻塞当前线程，如果没有指定 timeout 参数，当前线程将一直处于阻塞状态，直到 Future 代表的任务返回。

##  二、获取执行结果

程序调用了 Future 的 result() 方法来获取线程任务的运回值，但该方法会阻塞当前主线程，只有等到钱程任务完成后，result() 方法的阻塞才会被解除。

如果程序不希望直接调用 result() 方法阻塞线程，则可通过 Future 的 add_done_callback() 方法来添加回调函数，该回调函数形如 fn(future)。当线程任务完成后，程序会自动触发该回调函数，并将对应的 Future 对象作为参数传给该回调函数。

```
from concurrent.futures import ThreadPoolExecutor
import threading
import time


def action(max):
    '''
    准备作为线程任务的函数
    :param max:
    :return:
    '''
    sum = 0
    for i in range(max):
        print(threading.current_thread().name + ' ' + str(i))
        sum += i
    return sum


with ThreadPoolExecutor(max_workers=2) as pool:  # 创建包含2个线程的线程池
    future1 = pool.submit(action, 50)  # 向线程池提交一个task, 50会作为action()函数的参数
    future2 = pool.submit(action, 100)


    def getResult(future):
        print(future.result())


    future1.add_done_callback(getResult)
    future2.add_done_callback(getResult)
    print('---------------------------')
```

future1、future2 添加了同一个回调函数，该回调函数会在线程任务结束时获取其返回值。

由于程序并未直接调用 future1、future2 的 result() 方法，因此主线程不会被阻塞，可以立即看到输出主线程打印出的横线。接下来将会看到两个新线程并发执行，当线程任务执行完成后，get_result() 函数被触发，输出线程任务的返回值。

由于线程池实现了上下文管理协议（Context Manage Protocol），因此程序可以使用 with 语句来管理线程池，这样即可避免手动关闭线程池。

Exectuor 还提供了一个 map(func, *iterables, timeout=None, chunksize=1) 方法，该方法的功能类似于全局函数 map()，区别在于线程池的 map() 方法会为 iterables 的每个元素启动一个线程，以并发方式来执行 func 函数。这种方式相当于启动 len(iterables) 个线程，井收集每个线程的执行结果。

```
from concurrent.futures import ThreadPoolExecutor
import threading
import time


def action(max):
    '''
    准备作为线程任务的函数
    :param max:
    :return:
    '''
    sum = 0
    for i in range(max):
        print(threading.current_thread().name + ' ' + str(i))
        sum += i
    return sum


with ThreadPoolExecutor(max_workers=4) as pool:  # 创建包含4个线程的线程池
    results=pool.map(action,(50,100,150)) # 元组有3个元素，因此程序启动3条线程来执行action函数
    print('----------------------------')
    for i in results:
        print(i)
```

map() 方法的返回值将会收集每个线程任务的返回结果。
虽然程序会以并发方式来执行 action() 函数，但最后收集的 action() 函数的执行结果，依然与传入参数的结果保持一致。


