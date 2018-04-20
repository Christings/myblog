---
title: Python中的进程与线程
date: 2018-03-27 14:35:23
tags:
---
## 一、多进程

	Python中实现多进程有2种方式：
		os模块中的fork()方法
		使用multiprocessing模块

	区别：
		fork()仅仅适用于Unix/Linux操作系统，不支持Windows
		multiprocessing支持跨平台
### 1.使用os模块的fork实现多进程
	fork方法来源于Unix/Linux中提供的fork系统调用，此方法很特殊。
	普通方法调用一次，返回一次，fork方法调用一次，返回两次。
	原因：操作系统将当前进程（父进程）复制出一份进程（子进程），两个进程几乎完全相同，于是fork方法分别在父进程和子进程中分别返回。
	子进程中永远返回0，父进程中返回子进程的ID。

	getpid() -- 获取当前进程的ID
	getppid() -- 获取父进程的ID

----------

    `import os
	if __name__ =='__main__':
	    print('current Process (%s) start ...',(os.getpid()))
	    pid=os.fork()
	    if pid<0:
	        print('error in fork')
	    elif pid==0:
	        print('I am child process(%s) and my parent process is (%s)',(os.getpid(),os.getppid()))
	    else:
	        print('I (%s) created a child process (%s).',(os.getpid(),pid))`

----------
	输出：
	current Process (%s) start ... 17441
	I (%s) created a child process (%s). (17441, 20736)
	I am child process(%s) and my parent process is (%s) (20736, 17441)
### 2.使用multiprocessing模块创建多进程

	multiprocessing模块提供Process类来描述一个进程对象，当创建子进程时，
	只需要传入一个执行函数和函数参数，即可完成Process实例的创建，再用start
	()方法启动进程，用join()方法实现进程间的同步。

----------
	`import os
	from multiprocessing import Process
	# 子进程要执行的代码
	def run_proc(name):
	    print('Child process %s (%s) Running...'%(name,os.getpid()))
	if __name__ =='__main__':
	    print('Parent Process %s.'%(os.getpid()))
	    for i in range(5):
	        p=Process(target=run_proc,args=(str(i),))
	        print('Process will start')
	        p.start()
	    p.join()
	    print('Process end.')`

----------
	
	输出：
	Parent Process 17441.
	Process will start
	Process will start
	Process will start
	Child process 0 (38587) Running...
	Process will start
	Child process 1 (38590) Running...
	Process will start
	Child process 2 (38591) Running...
	Child process 3 (38596) Running...
	Child process 4 (38599) Running...
	Process end.
### 3.使用multiprocessing模块的Pool类来代表进程池对象
	如果要启动大量的子进程，使用进程池批量创建子进程的方式更加常见。
	Pool可以提供指定数量的进程供用户调用，默认大小是CPU核数。
	当新请求提交到Pool中时，如果池未满，就会创建新进程来执行该请求；
	但如果池中进程数已达到最大值，请求就会等待，直到池中有进程结束，才会创建新进程来处理。
	注意：Pool对象调用join()方法会等待所有子进程执行完毕，调用join()之前须
	调用close()，调用close()后就不能添加新的Process
	
----------


	`import os,time,random
	from multiprocessing import Pool
	
	def run_task(name):
	    print('Task %s (pid=%s) is Running...'%(name,os.getpid()))
	    time.sleep(random.random()*3)
	    print('Task %s end.'%name)
	    
	if __name__ =='__main__':
	    print('Current Process %s.'%(os.getpid()))
	    p=Pool(processes=3)
	    for i in range(5):
	        p.apply_async(run_task,args=(i,))
	    print('Waiting for all subprocesses done...')
	    p.close()
	    p.join()
	    print('All subprocess done.')`	

----------
	输出:
	Current Process 17441.
	Task 2 (pid=40820) is Running...
	Task 1 (pid=40819) is Running...
	Task 0 (pid=40818) is Running...
	Waiting for all subprocesses done...
	Task 2 end.
	Task 3 (pid=40820) is Running...
	Task 0 end.
	Task 4 (pid=40818) is Running...
	Task 1 end.
	Task 4 end.
	Task 3 end.
	All subprocess done.
### 4.进程间通信
	
	Python进程通信方式：Queue、Pipe、Value+Array
	区别：Pipe常用于两个进程间通信，Queue用于多个进程间通信
	Queue：put和get
#### 4.1 Queue
----------
    `# ----Queue----
	import os,time,random
	from multiprocessing import Process,Queue
	
	# 写数据进程执行的代码：
	def proc_write(q,urls):
	    print('Process (%s) is writing...'%os.getpid())
	    for url in urls:
	        q.put(url)
	        print('Put %s to queue...'%url)
	        time.sleep(random.random())
	
	# 读数据进程执行的代码：
	def proc_read(q):
	    print('Process (%s) is reading...'%os.getpid())
	    while True:
	        url=q.get(True)
	        print('Get %s from queue.'%url)
	    
	if __name__ =='__main__':
	    # 父进程创建Queue，并传给各个子进程：
	    q=Queue()
	    proc_writer1=Process(target=proc_write,args=(q,['url_1','url_2','url_3']))
	    proc_writer2=Process(target=proc_write,args=(q,['url_4','url_5','url_6']))
	    proc_reader=Process(target=proc_read,args=(q,))
	    # 启动子进程proc_writer,写入：
	    proc_writer1.start()
	    proc_writer2.start()
	    # 启动子进程proc_reader,读取：
	    proc_reader.start()
	    # 等待proc_writer结束：
	    proc_writer1.join()
	    proc_writer2.join()
	    # proc_reader进程里是死循环，无法等待其结束，只能强行终止：
	    proc_reader.terminate()`

----------
	
	输出：
	Process (45109) is writing...
	Process (45110) is writing...
	Put url_1 to queue...
	Put url_4 to queue...
	Process (45113) is reading...
	Get url_1 from queue.
	Get url_4 from queue.
	Put url_5 to queue...
	Get url_5 from queue.
	Put url_2 to queue...
	Get url_2 from queue.
	Put url_6 to queue...
	Get url_6 from queue.
	Put url_3 to queue...
	Get url_3 from queue.

#### 4.2 Pipe

    `# ----Pipe----
	import os,time,random
	import multiprocessing
	
	def proc_send(pipe,urls):
	    for url in urls:
	        print('Process (%s) send: %s'%(os.getpid(),url))
	        pipe.send(url)
	        time.sleep(random.random())
	
	def proc_recv(pipe):
	    while True:
	        print('Process (%s) recv: %s'%(os.getpid(),pipe.recv()))
	        time.sleep(random.random())
	        
	if __name__ =='__main__':
	    pipe=multiprocessing.Pipe()
	    p1=multiprocessing.Process(target=proc_send,args=(pipe[0],['url_'+str(i) for i in range(10) ]))
	    p2=multiprocessing.Process(target=proc_recv,args=(pipe[1],))
	    
	    p1.start()
	    p2.start()
	    
	    p1.join()
	    p2.terminate()`

----------
	输出：
	Process (15338) send: url_0
	Process (15339) recv: url_0
	Process (15338) send: url_1
	Process (15339) recv: url_1
	Process (15338) send: url_2
	Process (15339) recv: url_2
	Process (15338) send: url_3
	Process (15339) recv: url_3
	Process (15338) send: url_4
	Process (15338) send: url_5
	Process (15339) recv: url_4
	Process (15339) recv: url_5
	Process (15338) send: url_6
	Process (15339) recv: url_6
	Process (15338) send: url_7
	Process (15339) recv: url_7
	Process (15338) send: url_8
	Process (15339) recv: url_8
	Process (15338) send: url_9
## 二、多线程
	
	Python中提供2个模块实现多线程：thread和threading
	thread属于低级模块，threading是高级模块，对thread进行了封装
	大多数使用threading模块
### 1.用threading模块创建多线程
	两种方式：
		函数传入并创建Thread实例，调用start方法开始执行
		直接从threading.Thread继承并创建线程类，重写__init__和run方法
#### 1.1 函数传入
    `import random 
	import time,threading
	
	# 新线程执行的代码：
	def thread_run(urls):
	    print('Current %s is running...'%threading.current_thread().name)
	    for url in urls:
	        print('%s---->>> %s'%(threading.current_thread().name,url))
	        time.sleep(random.random())
	    print('%s ended.'%threading.current_thread().name)
	
	print('%s is running...'%threading.current_thread().name)
	t1=threading.Thread(target=thread_run,name='Thread_1',args=(['url_1','url_2','url_3'],))
	t2=threading.Thread(target=thread_run,name='Thread_2',args=(['url_4','url_5','url_6'],))
	t1.start()
	t2.start()
	t1.join()
	t2.join()
	print('%s ended.'%threading.current_thread().name)`

----------
	输出：
	MainThread is running...
	Current Thread_1 is running...
	Thread_1---->>> url_1
	Current Thread_2 is running...
	Thread_2---->>> url_4
	Thread_2---->>> url_5
	Thread_1---->>> url_2
	Thread_1---->>> url_3
	Thread_2---->>> url_6
	Thread_2 ended.
	Thread_1 ended.
	MainThread ended.
#### 1.2 直接继承
    `import random 
	import time,threading
	
	class myThread(threading.Thread):
	    def __init__(self,name,urls):
	        threading.Thread.__init__(self,name=name)
	        self.urls=urls
	    
	    def run(self):
	        print('Current %s is running...'%threading.current_thread().name)
	        for url in self.urls:
	            print('%s---->>> %s'%(threading.current_thread().name,url))
	            time.sleep(random.random())
	        print('%s ended.'%threading.current_thread().name)   
	
	print('%s is running...'%threading.current_thread().name)
	t1=myThread(name='Thread_1',urls=['url_1','url_2','url_3'])
	t2=myThread(name='Thread_2',urls=['url_4','url_5','url_6'])
	t1.start()
	t2.start()
	t1.join()
	t2.join()
	print('%s ended.'%threading.current_thread().name)`

----------
	输出：
	MainThread is running...
	Current Thread_1 is running...
	Current Thread_2 is running...
	Thread_2---->>> url_4
	Thread_1---->>> url_1
	Thread_1---->>> url_2
	Thread_2---->>> url_5
	Thread_2---->>> url_6
	Thread_1---->>> url_3
	Thread_2 ended.
	Thread_1 ended.
	MainThread ended.
### 2.线程同步
	Thread对象的Lock和RLock可实现线程同步，2个对象均有acquire和release方法
	`import random 
	import time,threading
	
	mylock=threading.RLock()
	num=0
	
	class myThread(threading.Thread):
	    def __init__(self,name):
	        threading.Thread.__init__(self,name=name)
	    
	    def run(self):
	        global num
	        while True:
	            mylock.acquire()
	            print('%s locked,Number:%d'%(threading.current_thread().name,num))
	            if num>=4:
	                mylock.release()
	                print('%s released,Number:%d'%(threading.current_thread().name,num))
	                break
	            num+=1
	            print('%s released,Number:%d'%(threading.current_thread().name,num))
	            mylock.release()
	if __name__=='__main__':
	    t1=myThread(name='Thread_1')
	    t2=myThread(name='Thread_2')
	    t1.start()
	    t2.start()`

----------
	输出：
	Thread_1 locked,Number:0
	Thread_1 released,Number:1
	Thread_1 locked,Number:1
	Thread_1 released,Number:2
	Thread_1 locked,Number:2
	Thread_1 released,Number:3
	Thread_1 locked,Number:3
	Thread_1 released,Number:4
	Thread_1 locked,Number:4
	Thread_1 released,Number:4
	Thread_2 locked,Number:4
	Thread_2 released,Number:4
## 三、协程
	Python中通过yield提供对协程的基本支持，但不完全。
	所以我们用第三方geven库来使用协程。
	spawn用来形成协程，joinall用来添加协程任务。

----------

    `from gevent import monkey
	monkey.patch_all()
	import gevent
	import urllib.request
	
	def run_task(url):
	    print('Visit --> %s'%url)
	    try:
	        response=urllib.request.urlopen(url)
	        data=response.read()
	        print('%d bytes received from %s.'%((len(data)),url))
	    except Exception as e:
	        print(e)
	        
	if __name__=='__main__':
	    urls=['https://github.com/','https://www.python.org/','https://www.baidu.com/']
	    greenlets=[gevent.spawn(run_task,url) for url in urls]
	    gevent.joinall(greenlets)`

----------
	输出：
	Visit --> https://github.com/
	Visit --> https://www.python.org/
	Visit --> https://www.baidu.com/
	227 bytes received from https://www.baidu.com/.
	52801 bytes received from https://github.com/.
	48922 bytes received from https://www.python.org/.

----------
    `# 提供对pool的支持，当拥有动态数量的greenlets需要进行并发管理
	from gevent import monkey
	monkey.patch_all()
	import gevent
	import urllib.request
	from gevent.pool import Pool
	
	def run_task(url):
	    print('Visit --> %s'%url)
	    try:
	        response=urllib.request.urlopen(url)
	        data=response.read()
	        print('%d bytes received from %s.'%((len(data)),url))
	    except Exception as e:
	        print(e)
	    print('url:%s --->finish'%url)
	        
	if __name__=='__main__':
	    pool=Pool()
	    urls=['https://github.com/','https://www.python.org/','https://www.baidu.com/']
	#     greenlets=[gevent.spawn(run_task,url) for url in urls]
	#     gevent.joinall(greenlets)
	    results=pool.map(run_task,urls)
	    print(results)`


----------
	输出：
	Visit --> https://github.com/
	Visit --> https://www.python.org/
	Visit --> https://www.baidu.com/
	227 bytes received from https://www.baidu.com/.
	url:https://www.baidu.com/ --->finish
	48922 bytes received from https://www.python.org/.
	url:https://www.python.org/ --->finish
	52801 bytes received from https://github.com/.
	url:https://github.com/ --->finish
	[None, None, None]
## 四、分布式进程
	写一个服务进程作为调度者，将任务分布到其他多个子进程中，依靠网络通信进行管理。
	步骤：
		未完--待续