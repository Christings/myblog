---
title: python并发-12.进程创建
date: 2020-02-26 20:51:12
tags: Python并发
categories:
        - Python
        - Python并发
---
## 一、os.fork()--在windows系统上无效
每个 Python 程序在执行时，系统都会生成一个新的进程，该进程又称父进程（或主进程）。

Python os 模块提供 fork() 函数，该函数可以在当前程序中再创建出一个进程（又称子进程）。

程序调用 os.fork() 函数，程序中会拥有 2 个进程，其中父进程负责执行整个程序代码，而通过 fork() 函数创建出的子进程，会从创建位置开始，执行后续所有的程序（包含创建子进程的代码）。

    注意：os.fork() 函数在 Windows 系统上无效，只在 UNIX 及类 UNIX 系统上（包括UNIX、Linux 和 Mac OS X）效。

fork()语法：pid = os.fork()

pid 作为函数的返回值，主进程和子进程都会执行该语句，但主进程执行 fork() 函数得到的 pid 值为非 0 值（其实是子进程的进程 ID），而子进程执行该语句得到的 pid 值为 0。因此，pid 常常作为区分父进程和子进程的标志。

在大多数操作系统中，都会为执行的进程配备唯一的 ID 号，os 模块提供了 getpid() 和 getppid() 函数，可分别用来获取当前进程的 ID 号和父进程的 ID 号。

```
import os

print('父进程 id=', os.getpid())
pid = os.fork()  # 创建子进程，下面代码会被两个进程执行
print('当前进程 id=', os.getpid(), ' pid=', pid)
if pid == 0:
    print('子进程 id=', os.getpid(), ' 父进程 id=', os.getppid())
else:
    print('父进程 id=', os.getpid(), ' pid=', pid)
```

输出：

    父进程 id= 10396
    当前进程 id= 10396  pid= 10397
    父进程 id= 10396  pid= 10397
    当前进程 id= 10397  pid= 0
    子进程 id= 10397  父进程 id= 10396

注意，程序 if 判断语句，通过判断 pid 值是否为 0，分别为父进程和 fork() 函数创建的子进程布置了不同的执行任务，即子进程负责执行 if 代码块，而父进程则负责执行 else 代码块。

## 二、Process
Python multiprocessing 模块提供了 Process 类，该类可用来在 Windows 平台上创建新进程。和使用 Thread 类创建多线程方法类似，使用 Process 类创建多进程也有以下 2 种方式：

    直接创建 Process 类的实例对象，由此就可以创建一个新的进程；

    通过继承 Process 类的子类，创建实例对象，也可以创建新的进程。注意，继承 Process 类的子类需重写父类的 run() 方法。

Process类常用属性和方法：
![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/process.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/process.png)

### 2.1 通过Process类创建进程

和使用 thread 类创建子线程的方式非常类似，使用 Process 类创建实例化对象，其本质是调用该类的构造方法创建新进程。Process 类的构造方法格式如下：

    def __init__(self,group=None,target=None,name=None,args=(),kwargs={})

参数的含义为：

    group：该参数未进行实现，不需要传参；
    target：为新建进程指定执行任务，也就是指定一个函数；
    name：为新建进程设置名称；
    args：为 target 参数指定的参数传递非关键字参数；
    kwargs：为 target 参数指定的参数传递关键字参数。

```
from multiprocessing import Process
import os

print('当前进程id：', os.getpid())


def action(name, *add):
    '''
    准备作为新进程的 target 参数
    :param name:
    :param add:
    :return:
    '''
    print(name)
    for arc in add:
        print('%s --当前进程%d' % (arc, os.getpid()))


if __name__ == '__main__':
    myTuple = ('python', 'shell', 'java')
    myProcess = Process(target=action, args=('myProcess进程', *myTuple))  # 创建子进程，执行 action 函数
    myProcess.start()  # 启动子进程
    action('主进程', *myTuple)  # 主进程执行 action 函数
```

输出：

    当前进程id： 68797
    主进程
    python --当前进程68797
    shell --当前进程68797
    java --当前进程68797
    myProcess进程
    python --当前进程68798
    shell --当前进程68798
    java --当前进程68798

说明：通过 multiprocessing.Process 来创建并启动进程时，程序必须先判断 if __name__=='__main__':，否则运行该程序会引发异常。

程序分别为主进程和我们创建的新进程，主进程会执行整个程序，而子进程不会执行 if __name__ == '__main__' 中包含的程序，而是先执行此判断语句之外的所有可执行程序，然后再执行我们分配让它的任务（也就是通过 target 参数指定的函数）。

### 2.2 通过Process继承类创建进程

注意：在创建 Process 的子类时，需在子类内容重写 run() 方法。实际上，该方法所起到的作用，就如同第一种创建方式中 target 参数执行的函数。

通过 Process 子类创建进程，和使用 Process 类一样，先创建该类的实例对象，然后调用 start() 方法启动该进程。

```
from multiprocessing import Process
import os

print('当前进程id：', os.getpid())


def action(name, *add):
    '''
    准备作为新进程的 target 参数
    :param name:
    :param add:
    :return:
    '''
    print(name)
    for arc in add:
        print('%s --当前进程%d' % (arc, os.getpid()))


class MyProcess(Process):
    '''
    定义进程类
    :param Process:
    :return:
    '''

    def __init__(self, name, *add):
        super().__init__()
        self.name = name
        self.add = add

    def run(self):
        print(self.name)
        for arc in self.add:
            print('%s --当前进程%d' % (arc, os.getpid()))


if __name__ == '__main__':
    myTuple = ('python', 'shell', 'java')
    myProcess = MyProcess('myprocess进程', *myTuple)
    myProcess.start()
    action('主进程', *myTuple)
```
输出：

    当前进程id： 80398
    主进程
    python --当前进程80398
    shell --当前进程80398
    java --当前进程80398
    myprocess进程
    python --当前进程80399
    shell --当前进程80399
    java --当前进程80399

运行结果与上一个程序的运行结果大致相同，它们只是创建进程的方式略有不同而已，推荐使用第一种来创建，更加简单。

## 三、进程启动
我们可以使用 os.fork() 和 Process 类来创建进程。
其中：

1、使用 os.fork() 函数创建的子进程，会从创建位置处开始，执行后续所有的程序，主进程如何执行，则子进程就如何执行；

2、而使用 Process 类创建的进程，则仅会执行if "__name__"="__main__"之外的可执行代码以及该类构造方法中 target 参数指定的函数（使用 Process 子类创建的进程，只能执行重写的 run() 方法）。

实际上，Python 创建的子进程执行的内容，和启动该进程的方式有关。而根据不同的平台，启动进程的方式大致可分为以下 3 种：

1、spawn：使用此方式启动的进程，只会执行和 target 参数或者 run() 方法相关的代码。Windows 平台只能使用此方法，事实上该平台默认使用的也是该启动方式。相比其他两种方式，此方式启动进程的效率最低。

2、fork：使用此方式启动的进程，基本等同于主进程（即主进程拥有的资源，该子进程全都有）。因此，该子进程会从创建位置起，和主进程一样执行程序中的代码。注意，此启动方式仅适用于 UNIX 平台，os.fork() 创建的进程就是采用此方式启动的。

3、forserver：使用此方式，程序将会启动一个服务器进程。即当程序每次请求启动新进程时，父进程都会连接到该服务器进程，请求由服务器进程来创建新进程。通过这种方式启动的进程不需要从父进程继承资源。注意，此启动方式只在 UNIX 平台上有效。

总的来说，使用类 UNIX 平台，启动进程的方式有以上 3 种，而使用 Windows 平台，只能选用 spawn 方式（默认即可）。

在了解以上 3 种进程启动方式的基础上，我们还需要知道手动设置进程启动方式的方法，大致有以下  2 种：

1、 Python multiprocessing 模块提供了一个set_start_method() 函数，该函数可用于设置启动进程的方式。需要注意的是，该函数的调用位置，必须位于所有与多进程有关的代码之前。

```
import multiprocessing
from multiprocessing import Process
import os

print('当前进程id：', os.getpid())


def action(name, *add):
    '''
    准备作为新进程的 target 参数
    :param name:
    :param add:
    :return:
    '''
    print(name)
    for arc in add:
        print('%s --当前进程%d' % (arc, os.getpid()))


if __name__ == '__main__':
    myTuple = ('python', 'shell', 'java')
    multiprocessing.set_start_method('spawn')  # 设置进程启动方式
    myProcess = Process(target=action, args=('myProcess进程', *myTuple))  # 创建子进程，执行 action 函数
    myProcess.start()  # 启动子进程
```
输出：

    当前进程id： 82570
    当前进程id： 82572
    myProcess进程
    python --当前进程82572
    shell --当前进程82572
    java --当前进程82572

注意：由于此程序中进程的启动方式为 spawn，因此该程序可以在任意（ Windows 和类 UNIX 上都可以 ）平台上执行。

2、除此之外，还可以使用 multiprocessing 模块提供的 get_context() 函数来设置进程启动的方法，调用该函数时可传入 "spawn"、"fork"、"forkserver" 作为参数，用来指定进程启动的方式。

注意：前面创建进程使用的 multiprocessing.Process() 这种形式，而在使用 get_context() 函数设置启动进程方式时，需用该函数的返回值，代替 multiprocessing 模块调用 Process()。 

```
import multiprocessing
from multiprocessing import Process
import os

print('当前进程id：', os.getpid())


def action(name, *add):
    '''
    准备作为新进程的 target 参数
    :param name:
    :param add:
    :return:
    '''
    print(name)
    for arc in add:
        print('%s --当前进程%d' % (arc, os.getpid()))


if __name__ == '__main__':
    myTuple = ('python', 'shell', 'java')
    ctx = multiprocessing.get_context('spawn')  # 设置进程启动方式
    myProcess = ctx.Process(target=action,
                            args=('myProcess进程', *myTuple))  # 用 ctx 代替 multiprocessing 模块创建子进程，执行 action() 函数
    myProcess.start()  # 启动子进程
```

输出：

    当前进程id： 83308
    当前进程id： 83310
    myProcess进程
    python --当前进程83310
    shell --当前进程83310
    java --当前进程83310

仅演示了在 Windows 平台上设置进程启动方式的效果，有兴趣的读者可自行尝试选择类 UNIX 平台测试其他启动进程的方式。
