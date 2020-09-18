---
title: python并发-10.定时器Timer
date: 2020-02-26 14:50:58
tags: Python并发
categories:
        - Python
        - Python并发
---
Thread 类有一个 Timer子类，该子类可用于控制指定函数在特定时间内执行一次。

```
from threading import Timer

def hi():
    print('hi')

t=Timer(10,hi) # 10秒后执行hi()
t.start()
```

注意：Timer 只能控制函数在指定时间内执行一次，如果要使用 Timer 控制函数多次重复执行，则需要再执行下一次调度。

```
from threading import Timer
import time
# 定义总共输出几次的计数器
count = 0
def print_time():
    print("当前时间：%s" % time.ctime())
    global t, count
    count += 1
    # 如果count小于10，开始下一次调度
    if count < 10:
        t = Timer(1, print_time)
        t.start()

t = Timer(1, print_time)
t.start()
```

如果要取消 Timer 的调度，则可调用 Timer 对象的 cancel() 函数