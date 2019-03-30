---
layout: post
title: Python线程&进程
category: 技术
tags: python,thread
keywords: thread
description: thread
---
### 线程&进程

GIL global interpreter lock GIL全局解释锁

python中一个线程对应于c中的线程

python是先将py文件编译为字节码

gil使得同一时刻只有一个线程在一个cpu上执行字节码，无法将多个线程映射到多个cpu上 

为了安全，但速度慢，无法体现多核优势。pypy是去gil化的。gil在遇到io操作会主动释放

  

操作系统调度的最小单元线程，对于io操作，多线程和多进程差别不大。

#### 通过thread类实例化

```
thread1 = threading.Thread(target=func, args=("",))

thread1.setDaemon(True)#设置为守护线程。当主线程关闭时，子线程被kill

thread1.join() #阻塞，等待thread1子线程执行完成
```

#### 继承thread来实现多线程
当代码量大，逻辑复杂时，推荐通过继承thread来实现多线程

```
class GetDetailHtml(threading.Thread):
    def __init__(self, name):   #为线程命名，好习惯
        super().__init__(name=name)
    
    def run(self):
        print('get detail html')
        time.sleep(2)
        print('get detail html done')

class GetTitleHtml(threading.Thread):
    def __init__(self, name):
        super().__init__(name=name)

    def run(self):
        print('get title url')
        time.sleep(2)
        print('get title url done')


if __name__ == '__main__':
    thread1 = GetTitleHtml('get title html')
    thread2 = GetDetailHtml('get detail html')
    start_time = time.time()
    thread1.start()
    thread2.start()
    thread1.join()
    thread2.join()
    print('last time {}'.format(time.time() - start_time))
```
  

### 线程之间的通信方式

#### 1.共享全局变量(list)（多线程可以，多进程不行）

这种方法，线程并不安全，需要加gil锁，所以并不推荐用作通信

```
import time
import threading

#1.共享全局变量
detail_url_list = []

def get_title_html(detail_url_list):
    print('get title url')
    time.sleep(2)
    for i in range(20):
        detail_url_list.append('http://www.test.com/id/{}/'.format(i))
    print('get title url done')

def get_detail_html(detail_url_list):
    print('get detail html')
    time.sleep(2)
    if len(detail_url_list):
        url = detail_url_list.pop()
        print('get detail html done')

if __name__ == '__main__':
    thread1 = threading.Thread(target=get_title_html, args=(detail_url_list,))
    for i in range(10): #启动10个线程，太多线程操作系统切换会产生很大代价
        html_thread = threading.Thread(target=get_detail_html, args=(detail_url_list,))
        html_thread.start()
    start_time = time.time()
    thread1.start()
    thread1.join()
    print('last time {}'.format(time.time() - start_time))
```

#### 2.通过queue方法进行线程间通讯,queue本身是线程安全的

```
from queue import Queue
import time
import threading

def get_title_html(queue):
    while True:
        print('get title url')
        time.sleep(2)
        for i in range(20):
            queue.put('http://www.test.com/id/{}/'.format(i))
        print('get title url done')

def get_detail_html(queue):
    while True:
        print('get detail html')
        time.sleep(2)
        url = queue.get()  #get方法是阻塞方法，如果queue为空就等待
        print('get detail html done')

if __name__ == '__main__':
    detail_queue = Queue(maxsize=1000)
    thread1 = threading.Thread(target=get_title_html, args=(detail_queue,))
    for i in range(10): #启动10个线程，太多线程操作系统切换会产生很大代价
        html_thread = threading.Thread(target=get_detail_html, args=(detail_queue,))
        html_thread.start()
    start_time = time.time()
    thread1.start()
    thread1.join()
    print('last time {}'.format(time.time() - start_time))


queue.get()#阻塞方法，如果为空就一直停顿等待
queue.put_nowait()#设置为非阻塞
queue.qsize() #获取长度，同样是线程安全的可以直接使用
queue.empty()
queue.full()
queue.join()#阻塞直到执行到task_done()
queue.task_done()
```
.join()一直阻塞，想退出需要调用.task_down() #爬虫暂停

如果共用变量为dict或其他非队列数据，也可以考虑使用全局变量


### 线程同步（lock, rlock）

避免淘宝库存同时有人买
线程同步机制,lock锁，反正锁住的代码段都只能有一个在运行，释放之后才能让其他代码运行.

使用锁会出现的问题
1. 用锁会影响性能
2. 锁会引起死锁（互相等待）,不能连续调用两次acquire

```
from threading import Lock

lock = Lock()

global lock

lock.acquire() #获取

a += 1

lock.release() #释放
```

死锁情况

```
A(a, b)
acquire(a)
acquire(b)

B(a, b)
acquire(b) # a,b同时等待
acquire(a)
```

#### rlock可重入的锁

解决lock，不能连续调用acquire的问题。它可以在同一个线程里面，可以连续调用多次acquire，需要相等数量的release.

```
from threading import RLock
lock = Rlock()

lock.acquire()
lock.acquire()
lock.release()
lock.release()
```

### condition条件变量

用于复杂的线程间同步锁


wait()允许等待某个条件变量的通知
notify()会通知调用了wait()方法的那个线程启动
1. 使用condition启动顺序非常重要
2. 一定要先调用with方法或者acquire和release，之后再调用wait(),notify()
3. condition有两层锁，一把底层锁会在线程调用wait方法的时候释放，上面的锁会值每次调用wait时分配一把并放入到cond的等待队列中，等到notify方法唤醒

```
import threading
from threading import Condition

class XiaAi(threading.Thread):
    def __init__(self, cond):
        super().__init__(name='小爱')
        self.cond = cond
    
    def run(self):
        with self.cond:
            self.cond.wait()
            print('{}: 在'.format(self.name))
            self.cond.notify()

            self.cond.wait()
            print('{}: 好啊'.format(self.name))
            self.cond.notify()
    
class TianMao(threading.Thread):
    def __init__(self, cond):
        super().__init__(name='天猫')
        self.cond = cond
    
    def run(self):
        with self.cond:
            print('{}: 小爱同学'.format(self.name))
            self.cond.notify()
            self.cond.wait()

            print('{}: 我们来对古诗吧'.format(self.name))
            self.cond.notify()
            self.cond.wait()

if __name__ == '__main__':
    cond = threading.Condition()

    xiaoai = XiaAi(cond)
    tianmao = TianMao(cond)

    xiaoai.start()
    tianmao.start()
    xiaoai.join()
    tianmao.join()
```


### Semaphore 控制线程并发数量

(文件读写，写一般只用一个线程，读可以允许有多个线程)

```
import threading
import time


class HtmlSpider(threading.Thread):
    def __init__(self, url, sem):
        super().__init__()
        self.url = url
        self.sem = sem
    
    def run(self):
        time.sleep(2)
        print('get html success')
        self.sem.release()

class UrlProducer(threading.Thread):
    def __init__(self, sem):
        super().__init__()
        self.sem = sem
    
    def run(self):
        for i in range(10):
            self.sem.acquire()
            html_thread = HtmlSpider('http://www.baidu.com/id/{}/'.format(i), self.sem)
            html_thread.start()

if __name__ == '__main__':
    sem = threading.Semaphore(3)
    url_thread = UrlProducer(sem)
    url_thread.start()
```
