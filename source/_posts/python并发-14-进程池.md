---
title: python并发-14.进程池
date: 2020-02-27 20:37:46
tags: Python并发
categories:
        - Python
        - Python并发
---
进程池可以提供指定数量的进程给用户使用，即当有新的请求提交到进程池中时，如果池未满，则会创建一个新的进程用来执行该请求；反之，如果池中的进程数已经达到规定最大值，那么该请求就会等待，只要池中有进程空闲下来，该请求就能得到执行。

Python multiprocessing 模块提供了 Pool() 函数，专门用来创建一个进程池，格式如下：
    
    multiprocessing.Pool( processes )

processes 参数用于指定该进程池中包含的进程数。如果进程是 None，则默认使用 os.cpu_count() 返回的数字（根据本地的 cpu 个数决定，processes 小于等于本地的 cpu 个数）。

注意：Pool() 函数只是用来创建进程池，而 multiprocessing 模块中表示进程池的类是 multiprocessing.pool.Pool 类。

multiprocessing.pool.Pool 类操作进程池方法：
![https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/process1.png](https://gypsy-1255824480.cos.ap-beijing.myqcloud.com/blog/process1.png)

```
from multiprocessing import Pool
import time
import os


def action(name='c++'):
    '''
    准备作为新进程的 target 参数
    :param name:
    :param add:
    :return:
    '''
    print(name, '--当前进程：', os.getpid())
    time.sleep(3)


if __name__ == '__main__':
    pool = Pool(processes=4)
    pool.apply_async(action)
    pool.apply_async(action, args=('python'))
    pool.apply_async(action, args=('java'))
    pool.apply_async(action, kwds={'name': 'shell'})
    pool.close()
    pool.join()

```

输出：

    c++ --当前进程： 5065
    shell --当前进程： 5068

我的电脑只有2核心。

使用 with 语句来管理进程池，则不用手动调用 close() 方法关闭进程池

```
from multiprocessing import Pool
import time
import os


def action(name='c++'):
    '''
    准备作为新进程的 target 参数
    :param name:
    :param add:
    :return:
    '''
    time.sleep(3)
    return (name, '--当前进程：', os.getpid())


if __name__ == '__main__':
    with Pool(processes=4) as pool:
        adds = pool.map(action, ('python', 'java', 'shell'))

    for arc in adds:
        print(arc)
```

输出：

    ('python', '--当前进程：', 5638)
    ('java', '--当前进程：', 5639)
    ('shell', '--当前进程：', 5640)

