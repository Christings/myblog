---
title: python并发-4.Thread的join()、daemon守护进程和sleep()
date: 2020-02-21 21:28:45
tags: Python并发
categories:
        - Python
        - Python并发
---
## 一、join()
《python并发-2.线程创建》这节中，程序的执行结果不唯一。原因是主线程 MainThread 和子线程 Thread-1 会轮流获得 CPU 资源，因此该程序的输出结果才会如此。

如果我们想让 Thread-1 子线程先执行，然后再让 MainThread 执行，可以通过调用线程对象的 join() 。

join() 方法的功能是在程序指定位置，优先让该方法的调用者使用 CPU 资源：

    thread.join( [timeout] )

thread 为 Thread 类或其子类的实例化对象；timeout 参数作为可选参数，其功能是指定 thread 线程最多可以霸占 CPU 资源的时间（以秒为单位），如果省略，则默认直到 thread 执行结束（进入死亡状态）才释放 CPU 资源。

```
import threading

# 定义线程调用的方法，*add可接收多个以非关键字方式传入的参数
def action(*add):
    for arc in add:
        # 调用 getName() 方法获取当前执行该程序的线程名
        print(threading.current_thread().getName() + ' ' + arc)


# 定义为线程方法传入的参数
myTuple = (
    "http://c.biancheng.net/python/",
    "http://c.biancheng.net/shell/",
    "http://c.biancheng.net/java/"
)

thread = threading.Thread(target=action, args=myTuple)  # 创建线程
thread.start() # 启动
thread.join() # 指定 thread 线程优先执行完毕

# 主线程执行
for i in range(5):
    print(threading.current_thread().getName())
```

输出：

    Thread-1 http://c.biancheng.net/python/
    Thread-1 http://c.biancheng.net/shell/
    Thread-1 http://c.biancheng.net/java/
    MainThread
    MainThread
    MainThread
    MainThread
    MainThread

thread 线程调用了 join() 方法，并且没有指定具体的 timeout 参数值。这意味着如果程序想继续往下执行，必须先执行完 thread 线程。

## 二、daemon守护进程
当程序中拥有多个线程时，主线程执行结束并不会影响子线程继续执行。换句话说，只有程序中所有线程全部执行完毕后，程序才算真正结束。

《python并发-2.线程创建》这节中包含了2个线程（MainThread和Thread-1），只有等 MatinThread 和 Thread-1 全部执行完之后，程序才执行结束。

除此之外，Python 还支持创建另一种线程，称为守护线程（或后台线程）。此类线程的特点是，当程序中主线程及所有非守护线程执行结束时，未执行完毕的守护线程也会随之消亡（进行死亡状态），程序将结束运行。

Python 解释器的垃圾回收机制就是守护线程的典型代表，当程序中所有主线程及非守护线程执行完毕后，垃圾回收机制也就没有再继续执行的必要了。

守护线程本质也是线程，因此其创建方式和普通线程一样，唯一不同之处在于，将普通线程设为守护线程，需通过线程对象调用其 damon 属性，将该属性的值该为 True。

注意：线程对象调用 daemon 属性必须在调用 start() 方法之前，否则 Python 解释器将报 RuntimeError 错误。

```
import threading

# 定义线程调用的方法，*add可接收多个以非关键字方式传入的参数
def action(*add):
    for arc in add:
        # 调用 getName() 方法获取当前执行该程序的线程名
        print(threading.current_thread().getName() + ' ' + arc)


# 定义为线程方法传入的参数
myTuple = (
    "http://c.biancheng.net/python/",
    "http://c.biancheng.net/shell/",
    "http://c.biancheng.net/java/"
)

thread = threading.Thread(target=action, args=myTuple)  # 创建线程
thread.daemon = True
thread.start()

# 主线程执行
for i in range(5):
    print(threading.current_thread().getName())

```

通过调用 thread 线程的 daemon 属性并赋值为 True，则该 thread 线程就变成了守护线程。由于该程序中除了 thread 守护线程就只有主线程 MainThread，因此只要主线程执行结束，则守护线程将随之消亡。

## 三、sleep():线程睡眠
位于 time 模块中的 sleep(secs) 函数，可以实现令当前执行的线程暂停 secs 秒后再继续执行。所谓暂停，即令当前线程进入阻塞状态，当达到 sleep() 函数规定的时间后，再由阻塞状态转为就绪状态，等待 CPU 调度。

```
import time

time.sleep(secs)
```

```
import threading
import time

# 定义线程调用的方法，*add可接收多个以非关键字方式传入的参数
def action(*add):
    for arc in add:
        time.sleep(1) # 暂停1s后执行
        # 调用 getName() 方法获取当前执行该程序的线程名
        print(threading.current_thread().getName() + ' ' + arc)


# 定义为线程方法传入的参数
myTuple = (
    "http://c.biancheng.net/python/",
    "http://c.biancheng.net/shell/",
    "http://c.biancheng.net/java/"
)

thread = threading.Thread(target=action, args=myTuple)  # 创建线程
thread.daemon = True
thread.start()

# 主线程执行
for i in range(5):
    print(threading.current_thread().getName())

```

输出：

    MainThread
    MainThread
    MainThread
    MainThread
    MainThread
    Thread-1 http://c.biancheng.net/python/
    Thread-1 http://c.biancheng.net/shell/
    Thread-1 http://c.biancheng.net/java/

和未使用 sleep() 函数的输出结果相比，显然主线程 MainThread 在前期获得 CPU 资源的次数更多，因为 Thread-1 线程中调用了 sleep() 函数，在一定程序上会阻碍该线程获得 CPU 调度。