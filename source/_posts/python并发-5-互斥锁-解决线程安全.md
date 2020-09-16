---
title: 'python并发-5.互斥锁:解决线程安全'
date: 2020-02-21 22:02:17
tags: python并发
categories:
        - python
        - python并发
---
多线程的优势：在于并发性，即可以同时运行多个任务。
多线程的劣势：但是当线程需要使用共享数据时，也可能会由于数据不同步产生“错误情况”，这是由系统的线程调度具有一定的随机性造成的。

互斥锁的作用就是解决数据不同步问题。

银行取钱（互斥锁）步骤：

    用户输入账户、密码，系统判断用户的账户、密码是否匹配。
    用户输入取款金额。
    系统判断账户余额是否大于取款金额。
    如果余额大于取款金额，则取款成功；如果余额小于取款金额，则取款失败。

一旦将这个流程放在多线程并发的场景下，就有【可能】出现问题。注意，此处说的是有可能，并不是一定。

取款程序:使用两个线程分别模拟两个人使用同一个账户做并发取钱操作

```
# 账户类，该账户类封装了账户编号和余额两个成员变量。
class Account:
    '''
    账户类
    '''
    def __init__(self,accountNo,balance):
        '''
        成员变量
        :param accountNo: 账户编号
        :param balance: 账户余额
        '''
        self.accountNo=accountNo
        self.balance=balance
```

```
# 模拟取钱的函数，该函数根据执行账户、取钱数量进行取钱操作，取钱的逻辑是当账户余额不足时无法提取现金，当余额足够时系统吐出钞票，余额减少。

import threading
import time
import Account


def draw(account, drawAccount):
    if account.balance >= drawAccount:
        print(threading.current_thread().name + '取钱成功，吐出钞票:' + str(drawAccount))
        time.sleep(0.01)
        account.balance -= drawAccount
        print('余额:' + str(account.balance))
    else:
        print(threading.current_thread().name + '取钱失败，余额不足.')


acct = Account.Account('1234567', 1000)
threading.Thread(name='甲', target=draw , args=(acct , 800)).start()
threading.Thread(name='乙', target=draw , args=(acct , 800)).start()
```

输出：

    甲取钱成功，吐出钞票:800
    乙取钱成功，吐出钞票:800
    余额:200
    余额:-600

人为地使用 time.sleep(0.001) 来强制线程调度切换，但这种切换也是完全可能发生的（100000 次操作只要有 1 次出现了错误，那就是由编程错误引起的）。

## 一、互斥锁同步线程

错误原因：run() 的方法体不具有线程安全性，程序中有两个并发线程在修改 Account 对象，而且系统恰好在 time.sleep(0.01) 处执行线程切换，切换到另一个修改 Account 对象的线程，所以就出现了问题。

threading模块：互斥锁（Lock）可解决上述问题。

threading模块中Lock类和RLock类都提供了如下两个方法来加互斥锁和释放互斥锁：

    acquire(blocking=True, timeout=-1)：请求对 Lock 或 RLock 加锁，其中 timeout 参数指定加锁多少秒。
    release()：释放锁。

Lock 和 RLock 的区别：

    threading.Lock：它是一个基本的锁对象，每次只能锁定一次，其余的锁请求，需等待锁释放后才能获取。

    threading.RLock：它代表可重入锁（Reentrant Lock）。对于可重入锁，在同一个线程中可以对它进行多次锁定，也可以多次释放。如果使用 RLock，那么 acquire() 和 release() 方法必须成对出现。如果调用了 n 次 acquire() 加锁，则必须调用 n 次 release() 才能释放锁。

所以，RLock 锁具有可重入性。即，同一个线程可以对已被加锁的 RLock 锁再次加锁，RLock 对象会维持一个计数器来追踪 acquire() 方法的嵌套调用，线程在每次调用 acquire() 加锁后，都必须显式调用 release() 方法来释放锁。所以，一段被锁保护的方法可以调用另一个被相同锁保护的方法。

Lock 是控制多个线程对共享资源进行访问的工具。通常，锁提供了对共享资源的独占访问，每次只能有一个线程对 Lock 对象加锁，线程在开始访问共享资源之前应先请求获得 Lock 对象。当对共享资源访问完成后，程序释放对 Lock 对象的锁定。

在实现线程安全的控制中，比较常用的是 RLock。代码如下：

```
class X:
    #定义需要保证线程安全的方法
    def m () :
        #加锁
        self.lock.acquire()
        try :
            #需要保证线程安全的代码
            ＃...方法体
        #使用finally 块来保证释放锁
        finally :
            #修改完成，释放锁
            self.lock.release()
```

使用 RLock 对象来控制线程安全，当加锁和释放锁出现在不同的作用范围内时，通常建议使用 finally 块来确保在必要时释放锁。

通过使用 Lock 对象可以非常方便地实现线程安全的类，线程安全的类具有如下特征：

    该类的对象可以被多个线程安全地访问。
    每个线程在调用该对象的任意方法之后，都将得到正确的结果。
    每个线程在调用该对象的任意方法之后，该对象都依然保持合理的状态。

总的来说，不可变类总是线程安全的，因为它的对象状态不可改变；但可变对象需要额外的方法来保证其线程安全。例如，上面的 Account 就是一个可变类，它的 self.accountNo和self._balance（为了更好地封装，将 balance 改名为 _balance）两个成员变量都可以被改变，当两个 线程同时修改 Account 对象的 self._balance 成员变量的值时，程序就出现了异常。下面将 Account 类对 self.balance 的访问设置成线程安全的，那么只需对修改 self.balance 的方法增加线程安全的控制即可。

```
import threading
import time


class Account:
    '''
    账户类
    '''

    def __init__(self, accountNo, balance):
        '''
        成员变量
        :param accountNo: 账户编号
        :param balance: 账户余额
        '''
        self.accountNo = accountNo
        self._balance = balance
        self.lock = threading.RLock()

    def getBalance(self):
        '''
        账户余额不允许随便修改，所以只为self._balance提供getter方法
        :return: 余额
        '''
        return self._balance

    def draw(self, drawAmount):
        self.lock.acquire()  # 加锁
        try:
            if self._balance >= drawAmount:
                print(threading.current_thread().name + '取钱成功，吐出钞票:' + str(drawAmount))
                time.sleep(0.01)
                self._balance -= drawAmount
                print('余额：' + str(self._balance))
            else:
                print(threading.current_thread().name + '取钱失败，余额不足。')
        finally:
            self.lock.release()  # 释放锁



def draw(account,drawAmout):
    account.draw(drawAmout)

acct=Account('1234567',1000)
threading.Thread(name='甲',target=draw,args=(acct,800)).start()
threading.Thread(name='乙',target=draw,args=(acct,800)).start()

```

程序中 RLock 对象作为同步锁，线程每次开始执行 draw() 方法修改 self.balance 时，都必须先对 RLock 对象加锁。当该线程完成对 self._balance 的修改，将要退出 draw() 方法时，则释放对 RLock 对象的锁定。这样的做法完全符合“加锁→修改→释放锁”的安全访问逻辑。

当一个线程在 draw() 方法中对 RLock 对象加锁之后，其他线程由于无法获取对 RLock 对象的锁定，因此它们同时执行 draw() 方法对 self._balance 进行修改。这意味着，并发线程在任意时刻只有一个线程可以进入修改共享资源的代码区（也被称为临界区），所以在同一时刻最多只有一个线程处于临界区内，从而保证了线程安全。

为了保证 Lock 对象能真正“锁定”它所管理的 Account 对象，程序会被编写成每个 Account 对象有一个对应的 Lock（就像一个房间有一个锁一样）。

Account 类增加了一个代表取钱的 draw() 方法，并使用 Lock 对象保证该 draw() 方法的线程安全，而且取消了 setBalance() 方法（避免程序直接修改 self._balance 成员变量），因此线程执行体只需调用 Account 对象的 draw() 方法即可执行取钱操作。

可变类的线程安全是以降低程序的运行效率作为代价的，为了减少线程安全所带来的负面影响，程序可以采用如下策略：

不要对线程安全类的所有方法都进行同步，只对那些会改变竞争资源（竞争资源也就是共享资源）的方法进行同步。例如，上面 Account 类中的 account_no 实例变量就无须同步，所以程序只对 draw() 方法进行了同步控制。

如果可变类有两种运行环境，单线程环境和多线程环境，则应该为该可变类提供两种版本，即线程不安全版本和线程安全版本。在单线程环境中使用钱程不安全版本以保证性能，在多线程环境中使用线程安全版本。





