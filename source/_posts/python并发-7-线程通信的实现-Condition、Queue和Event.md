---
title: python并发-7.线程通信的实现(Condition、Queue和Event)
date: 2020-02-23 21:58:38
tags: Python并发
categories:
        - Python
        - Python并发
---
## 一、Condition
当线程在系统中运行时，线程的调度具有一定的透明性，通常程序无法准确控制线程的轮换执行，如果有需要，Python 可通过线程通信来保证线程协调运行。

假设系统中有两个线程，这两个线程分别代表存款者和取钱者，现在假设系统有一种特殊的要求，即要求存款者和取钱者不断地重复存款、取钱的动作，而且要求每当存款者将钱存入指定账户后，取钱者就立即取出该笔钱。不允许存款者连续两次存钱，也不允许取钱者连续两次取钱。

为了实现这种功能，可以借助于 Condition 对象来保持协调。使用 Condition 可以让那些己经得到 Lock 对象却无法继续执行的线程释放 Lock 对象，Condition 对象也可以唤醒其他处于等待状态的线程。

将 Condition 对象与 Lock 对象组合使用，可以为每个对象提供多个等待集（wait-set）。因此，Condition 对象总是需要有对应的 Lock 对象。从 Condition 的构造器 __init__(self, lock=None) 可以看出，程序在创建 Condition 时可通过 lock 参数传入要绑定的 Lock 对象；如果不指定 lock 参数，在创建 Condition 时它会自动创建一个与之绑定的 Lock 对象。

Condition 类提供了如下几个方法：

    acquire([timeout])/release()：调用 Condition 关联的 Lock 的 acquire() 或 release() 方法。

    wait([timeout])：导致当前线程进入 Condition 的等待池等待通知并释放锁，直到其他线程调用该 Condition 的 notify() 或 notify_all() 方法来唤醒该线程。在调用该 wait() 方法时可传入一个 timeout 参数，指定该线程最多等待多少秒。

    notify()：唤醒在该 Condition 等待池中的单个线程并通知它，收到通知的线程将自动调用 acquire() 方法尝试加锁。如果所有线程都在该 Condition 等待池中等待，则会选择唤醒其中一个线程，选择是任意性的。

    notify_all()：唤醒在该 Condition 等待池中等待的所有线程并通知它们。

Account类：

通过一个旗标来标识账户中是否已有存款，当旗标为 False 时，表明账户中没有存款，存款者线程可以向下执行，当存款者把钱存入账户中后，将旗标设为 True，并调用 Condition 的 notify() 或 notify_all() 方法来唤醒其他线程。

当存款者线程进入线程体后，如果旗标为 True，就调用 Condition 的 wait() 方法让该线程等待。当旗标为 True 时，表明账户中已经存入了钱，取钱者线程可以向下执行，当取钱者把钱从账户中取出后，将旗标设为 False，并调用 Condition 的 notify() 或 notify_all() 方法来唤醒其他线程；当取钱者线程进入线程体后，如果旗标为 False，就调用 wait() 方法让该线程等待。
    
    draw():取钱
    deposit():存钱
    
    这两个方法可能需要并发修改 Account 类的 self.balance 成员变量的值，所以它们都使用 Lock 来控制线程安全。
    除此之外，这两个方法还使用了 Condition 的 wait() 和 notify_all() 来控制线程通信。

```
import threading


class Account:
    '''
    定义账户类
    '''

    def __init__(self, accountNo, balance):
        '''
        封装账户编号、账户余额两个成员变量
        :param accountNo:
        :param balance:
        :param _flag: 代表是否已经存钱的标识
        '''
        self.accountNo = accountNo
        self._balance = balance
        self.cond = threading.Condition()
        self._flag = False

    def getBalance(self):
        '''
        账户余额不允许随便修改，所以只为self._balance提供getter方法
        :return:
        '''
        return self._balance

    def draw(self, drawAmount):
        '''
        取钱
        :param drawAmount:
        :return:
        '''
        self.cond.acquire()  # 加锁，相当于调用Condition绑定的Lock的acquire()
        try:
            if not self._flag:  # 如果self._flag为假，表明账户中还没有人存钱进去，取钱方法阻塞
                self.cond.wait()
            else:
                print(threading.current_thread().name + '取钱：' + str(drawAmount))
                self._balance -= drawAmount
                print('账户余额:' + str(self._balance))
                self._flag = False  # 将标识账户是否已有存款的旗标设为False
                self.cond.notify_all()  # 唤醒其他线程
        finally:
            self.cond.release()  # 释放锁

    def desposit(self, depositAmount):
        '''
        存钱
        :param depositAmount:
        :return:
        '''
        self.cond.acquire()  # 加锁，相当于调用Condition绑定的Lock的acquire()
        try:
            if self._flag:  # 如果self._flag为真，表明账户中已有人存钱进去，存钱方法阻塞
                self.cond.wait()
            else:
                print(threading.current_thread().name + '存钱：' + str(depositAmount))
                self._balance += depositAmount
                print('账户余额:' + str(self._balance))
                self._flag = True  # 将表示账户是否已有存款的旗标设为True
                self.cond.notify_all()  # 唤醒其他线程
        finally:
            self.cond.release()  # 释放锁

```

对存款者线程而言，当程序进入 deposit() 方法后，如果 self._flag 为 True，则表明账户中已有存款，程序调用 Condition 的 wait() 方法被阻塞；否则，程序向下执行存款操作，当存款操作执行完成后，系统将 self._flag 设为 True，然后调用 notify_all() 来唤醒其他被阻塞的线程。如果系统中有存款者线程，存款者线程也会被唤醒，但该存款者线程执行到 ① 号代码处时再次进入阻塞状态，只有执行 draw() 方法的取钱者线程才可以向下执行。同理，取钱者线程的运行流程也是如此。

存款者线程循环 100 次重复存款，而取钱者线程则循环 100 次重复取钱，存款者线程和取钱者线程分别调用 Account 对象的 deposit()、draw() 方法来实现。主程序可以启动任意多个“存款”线程和“取钱”线程，可以看到所有的“取钱”线程必须等“存款”线程存钱后才可以向下执行，而“存款”线程也必须等“取钱”线程取钱后才可以向下执行。主程序代码如下：

```
def draw_many(account, drawAmount, max):
    '''
    模拟重复max次取钱操作
    :param account:
    :param drawAmount:
    :return:
    '''
    for i in range(max):
        account.draw(drawAmount)


def deposit_many(account, depositAmout, max):
    '''
    模拟重复max次存款操作
    :param account:
    :param depositAmout:
    :return:
    '''
    for i in range(max):
        account.deposit(depositAmout)


acct = Account('12345', 0)  # 创建账户
threading.Thread(name='取钱者', target=draw_many, args=(acct, 800, 100)).start()  # 创建、并启动一个“取钱”线程
threading.Thread(name='存款者甲', target=deposit_many, args=(acct, 800, 100)).start()  # 创建、并启动一个“取钱”线程
threading.Thread(name='存款者乙', target=deposit_many, args=(acct, 800, 100)).start()
threading.Thread(name='存款者丙', target=deposit_many, args=(acct, 800, 100)).start()

```

只有当取钱者线程取钱后，存款者线程才可以存钱；同理，只有等存款者线程存钱后，取钱者线程才可以取钱。

程序最后被阻塞无法继续向下执行。这是因为 3 个存款者线程共有 300 次尝试存钱操作，但 1 个取钱者线程只有 100 次尝试取钱操作，所以程序最后被阻塞。

此处的阻塞并不是死锁，对于这种情况，取钱者线程已经执行结束，而存款者线程只是在等待其他线程来取钱而已，并不是等待其他线程释放同步监视器。不要把死锁和程序阻塞等同起来。

## 二、Queue

queue 模块下提供了几个阻塞队列，这些队列主要用于实现线程通信。在 queue 模块下主要提供了三个类，分别代表三种队列，其主要区别就在于进队列、出队列的不同。

关于三个队列类介绍如下：

1、queue.Queue(maxsize=0)：代表 FIFO（先进先出）的常规队列，maxsize 可以限制队列的大小。如果队列的大小达到队列的上限，就会加锁，再次加入元素时就会被阻塞，直到队列中的元素被消费。如果将 maxsize 设置为 0 或负数，则该队列的大小就是无限制的。

2、queue.LifoQueue(maxsize=0)：代表 LIFO（后进先出）的队列，与 Queue 的区别就是出队列的顺序不同。

3、PriorityQueue(maxsize=0)：代表优先级队列，优先级最小的元素先出队列。

三个队列类的属性和方法基本相同， 它们都提供了如下属性和方法：

    Queue.qsize()：返回队列的实际大小，也就是该队列中包含几个元素。
    Queue.empty()：判断队列是否为空。
    Queue.full()：判断队列是否已满。
    Queue.put(item, block=True, timeout=None)：向队列中放入元素。如果队列己满，且 block 参数为 True（阻塞），当前线程被阻塞，timeout 指定阻塞时间，如果将 timeout 设置为 None，则代表一直阻塞，直到该队列的元素被消费；如果队列己满，且 block 参数为 False（不阻塞），则直接引发 queue.FULL 异常。
    Queue.put_nowait(item)：向队列中放入元素，不阻塞。相当于在上一个方法中将 block 参数设置为 False。
    Queue.get(item, block=True, timeout=None)：从队列中取出元素（消费元素）。如果队列已满，且 block 参数为 True（阻塞），当前线程被阻塞，timeout 指定阻塞时间，如果将 timeout 设置为 None，则代表一直阻塞，直到有元素被放入队列中； 如果队列己空，且 block 参数为 False（不阻塞），则直接引发 queue.EMPTY 异常。
    Queue.get_nowait(item)：从队列中取出元素，不阻塞。相当于在上一个方法中将 block 参数设置为 False。

```
import threading
import time
import queue


def product(bq):
    str_tuple = ('python', 'java', 'c++')
    for i in range(99999):
        print(threading.current_thread().name + '生产者准备生产元组元素')
        time.sleep(0.2)
        bq.put(str_tuple[i % 3])  # 尝试放入元素，如果队列已满，则线程被阻塞
        print(threading.current_thread().name + '生产者生产元素完成')


def consume(bq):
    while True:
        print(threading.current_thread().name + '消费者准备消费元组元素')
        time.sleep(0.2)
        t = bq.get()  # 尝试取出元素，如果队列已空，则线程被阻塞
        print(threading.current_thread().name + '消费者消费[ %s ]元素完成' % t)


bq = queue.Queue(maxsize=1)  # 容量为1的Queue
threading.Thread(target=product, args=(bq,)).start()
threading.Thread(target=product, args=(bq,)).start()
threading.Thread(target=product, args=(bq,)).start()
threading.Thread(target=consume, args=(bq,)).start()

```

启动三个生产者线程向 Queue 队列中放入元素，启动一个消费者线程从 Queue 队列中取出元素。本程序中 Queue 队列的大小为 1，因此三个生产者线程无法连续放入元素，必须等待消费者线程取出一个元素后，其中的一个生产者线程才能放入一个元素。

三个生产者线程都想向 Queue 中放入元素，但只要其中一个生产者线程向该队列中放入元素之后，其他生产者线程就必须等待，等待消费者线程取出 Queue 队列中的元素。

## 三、Event

Event 是一种非常简单的线程通信机制，一个线程发出一个 Event，另一个线程可通过该 Event 被触发。

Event 本身管理一个内部旗标，程序可以通过 Event 的 set() 方法将该旗标设置为 True，也可以调用 clear() 方法将该旗标设置为 False。程序可以调用 wait() 方法来阻塞当前线程，直到 Event 的内部旗标被设置为 True。

Event 提供了如下方法：

    is_set()：该方法返回 Event 的内部旗标是否为True。
    set()：该方法将会把 Event 的内部旗标设置为 True，并唤醒所有处于等待状态的线程。
    clear()：该方法将 Event 的内部旗标设置为 False，通常接下来会调用 wait() 方法来阻塞当前线程。
    wait(timeout=None)：该方法会阻塞当前线程。

```
import threading
import time

event = threading.Event()


def cal(name):
    # 等待事件，进入等待阻塞状态
    print('%s 启动' % threading.currentThread().getName())
    print('%s 准备开始计算状态' % name)
    event.wait()
    # 收到事件后进入运行状态
    print('%s 收到通知了.' % threading.currentThread().getName())
    print('%s 正式开始计算.' % name)


# 创建并启动两条，它们都会wait()处等待
threading.Thread(target=cal, args=('甲')).start()
threading.Thread(target=cal, args=('乙')).start()
time.sleep(2)
print('---------------------')
print('主线程发出事件') # 发出事件
event.set()
```

输出：
    Thread-1 启动
    甲 准备开始计算状态
    Thread-2 启动
    乙 准备开始计算状态
    ------------------
    主线程发出事件
    Thread-1 收到通知了.
    Thread-2 收到通知了.
    甲 正式开始计算！
    乙 正式开始计算！

以 cal() 函数为 target，创建并启动了两个线程。由于 cal() 函数调用了 Event 的 wait()，因此两个线程执行到 wait() 处都会进入阻塞状态；即使主线程在 time.sleep() 处被阻塞，两个子线程也不会向下执行。

直到主程序执行到最后一行，程序调用了 Event 的 set() 方法将 Event 的内部旗标设直为 True，并唤醒所有等待的线程，这两个线程才能向下执行。

以上内容没有使用 Event 的内部旗标，如果结合 Event 的内部旗标，同样可实现前面的 Account 的生产者-消费者效果：存钱线程（生产者）存钱之后，必须等取钱线程（消费者）取钱之后才能继续向下执行。

Event 实际上优点类似于 Condition 和旗标的结合体，但 Event 本身并不带 Lock 对象，因此如果要实现线程同步，还需要额外的 Lock 对象。

```
import threading


class Account:
    '''
    定义账户类
    '''

    def __init__(self, accountNo, balance):
        '''
        封装账户编号、账户余额两个成员变量
        :param accountNo:
        :param balance:
        :param _flag: 代表是否已经存钱的标识
        '''
        self.accountNo = accountNo
        self._balance = balance
        self.lock = threading.Lock()
        self.event = threading.Event()

    def getBalance(self):
        '''
        账户余额不允许随便修改，所以只为self._balance提供getter方法
        :return:
        '''
        return self._balance

    def draw(self, drawAmount):
        '''
        取钱
        :param drawAmount:
        :return:
        '''
        self.lock.acquire()  # 加锁
        if self.event.is_set():  # 如果Event内部旗标为True，表明账户中已有人存钱进去
            print(threading.current_thread().name + '取钱：' + str(drawAmount))
            self._balance -= drawAmount
            print('账户余额:' + str(self._balance))
            self.event.clear()  # 将Event内部旗标设为False
            self.lock.release()  # 释放锁
            self.event.wait()  # 阻塞当前线程
        else:
            self.lock.release()  # 释放锁
            self.event.wait()  # 阻塞当前线程


def desposit(self, depositAmount):
    '''
    存钱
    :param depositAmount:
    :return:
    '''
    self.lock.acquire()  # 加锁，相当于调用Condition绑定的Lock的acquire()
    if not self.event.is_set:  # 如果Event内部旗标为False，表明账户中还没有人存钱进去
        print(threading.current_thread().name + '存钱：' + str(depositAmount))
        self._balance += depositAmount
        print('账户余额:' + str(self._balance))
        self.event.set()  # 将Event内部旗标设为True
        self.lock.release()  # 释放锁
        self.event.wait()  # 阻塞当前线程
    else:
        self.lock.release()  # 释放锁
        self.event.wait()  # 阻塞当前线程
```


