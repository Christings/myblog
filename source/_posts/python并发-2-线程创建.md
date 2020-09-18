---
title: python并发-2.线程创建
date: 2020-02-21 20:29:32
tags: python并发
tags: Python并发
categories:
        - Python
        - Python并发
---
python中线程模块：
    
    _thread:提供了低级别的、原始的线程支持，以及一个简单的锁。功能比较有限，正如它的名字所暗示的（以 _ 开头），一般不建议使用 thread 模块；

    threading:功能丰富的多线程支持，推荐使用。

## 一、线程创建
1.使用 threading 模块中 Thread 类的构造器创建线程。即直接对类 threading.Thread 进行实例化创建线程，并调用实例化对象的 start() 方法启动线程。

2.继承 threading 模块中的 Thread 类创建线程类。即用 threading.Thread 派生出一个新的子类，将新建类实例化创建线程，并调用其 start() 方法启动线程。

### 1.1 调用Thread类的构造器创建线程
Thread 类提供了如下的 __init__() 构造器，可以用来创建线程：
    
    __init__(self, group=None, target=None, name=None, args=(), kwargs=None, *,daemon=None)

    此构造方法中，以上所有参数都是可选参数，即可以使用，也可以忽略。
    其中各个参数的含义如下：
    
    group：指定所创建的线程隶属于哪个线程组（此参数尚未实现，无需调用）；
    target：指定所创建的线程要调度的目标方法（最常用）；
    args：以元组的方式，为 target 指定的方法传递参数；
    kwargs：以字典的方式，为 target 指定的方法传递参数；
    daemon：指定所创建的线程是否为后代线程。

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
thread.start()

# for i in range(5):
#     print(threading.current_thread().getName())

```

输出：

    Thread-1 http://c.biancheng.net/python/
    Thread-1 http://c.biancheng.net/shell/
    Thread-1 http://c.biancheng.net/java/

    新创建的 thread 线程（线程名为 Thread-1）执行了 action() 函数。

默认情况下，主线程的名字为 MainThread，用户启动的多个线程的名字依次为 Thread-1、Thread-2、Thread-3、...、Thread-n 等。

    # for i in range(5):
    #     print(threading.current_thread().getName())

当前程序中有 2 个线程，分别为主线程 MainThread 和子线程 Thread-1，它们以并发方式执行，即 Thread-1 执行一段时间，然后 MainThread 执行一段时间。通过轮流获得 CPU 执行一段时间的方式，程序的执行在多个线程之间切换，从而给用户一种错觉，即多个线程似乎同时在执行。

如果程序中不显式创建任何线程，则所有程序的执行，都将由主线程 MainThread 完成，程序就只能按照顺序依次执行。

### 1.2 继承Thread类创建线程类

通过继承 Thread 类，我们可以自定义一个线程类，从而实例化该类对象，获得子线程。

注意：在创建 Thread 类的子类时，必须重写从父类继承得到的 run() 方法。因为该方法即为要创建的子线程执行的方法，其功能如同第一种创建方法中的 action() 自定义函数。

```
import threading


class MyThread(threading.Thread):
    '''
    创建子线程类，继承Thread
    '''

    def __init__(self, add):
        threading.Thread.__init__(self)
        self.add = add

    def run(self):
        '''
        重写run方法
        :return:
        '''
        for arc in self.add:
            # 调用 getName() 方法获取当前执行该程序的线程名
            print(threading.current_thread().getName() + ' ' + arc)


# 定义为 run() 方法传入的参数
myTuple = ("http://c.biancheng.net/python/",
           "http://c.biancheng.net/shell/",
           "http://c.biancheng.net/java/")

myThread = MyThread(myTuple)  # 创建子线程
myThread.start()  # 启动子线程
# 主线程执行此循环
for i in range(5):
    print(threading.current_thread().getName())

```

子线程 Thread-1 执行的是 run() 方法中的代码，而 MainThread 执行的是主程序中的代码，它们以快速轮换 CPU 的方式在执行。
    

