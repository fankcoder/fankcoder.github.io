---
layout: post
title: Python线程&进程&协程&asyncio
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

### 线程池concurrent #py3.2
concurrent容易的编写多线程，多进程代码

为什么使用线程池？

1.主线程中可以获取某一个线程的状态或者某一个任务的状态，以及返回值

2.当一个线程完成的时候，主线程能立即知道

3.futures可以让多线程多进程编码接口一致

```
from concurrent.futures import ThreadPoolExecutor,as_completed,wait,FIRST_COMPLETED
import time

def get_html(times):
    time.sleep(times)
    print('get html {} success'.format(times))
    return times

executor = ThreadPoolExecutor(max_workers=2)

#通过submit提交执行的函数到线程池中，submit是立即返回
task1 = executor.submit(get_html, (3))
task2 = executor.submit(get_html, (2))
print(task1.done())
#取消线程执行，状态done无论为true和false都不能取消执行。只有还没提交到线程池中的线程才能提交，与max_workers的设置有关
print(task2.cancel(2))
time.sleep(3.1)
#done()查看该线程当前的执行状态
print(task1.done())
#result()获取该线程的返回结果
print(task1.result())

#as_completed获取已经成功的task的返回，如爬虫
urls = [2,3,4,2]
all_task = [executor.submit(get_html, (url)) for url in urls]
for future in as_completed(all_task):
    data = future.result()
    print('get {} page success'.format(data))

#使用executor的map方法对as_completed进行简化.map的返回结果就是future.result()
#map的返回顺序是按照list的顺序，并不是先执行完就返回
for data in executor.map(get_html, urls):
    print('get {} page success'.format(data))
#wait设置阻塞，参数可选择条件默认为ALL_COMPLETED
wait(all_task, return_when=FIRST_COMPLETED)
print('main')
```

### 多进程

线程由于有gil无法并发，python多线程无法利用多cpu。

耗cpu的操作，多核cpu，计算，图像，挖矿多进程优于多线程。
io操作进程切换代价高于线程。

进程数据完全隔离,无法使用共享全局变量

子进程完全拷贝Fork之后的父进程代码运行

```
import time
from concurrent.futures import ThreadPoolExecutor,as_completed
from concurrent.futures import ProcessPoolExecutor

def fib(n):
    if n <=2:
        return 1
    return fib(n-1)+fib(n-2)

# 耗cpu的操作多进程比多线程速度快
with ProcessPoolExecutor(3) as executor:  #win使用多进程必须在__name=='__main__'下运行
# with ThreadPoolExecutor(3) as executor:
    alltask = [executor.submit(fib, (num)) for num in range(25,40)]
    start_time = time.time()
    for future in as_completed(alltask):
        data = future.result()
        print('exec result {}'.format(data))
    
    print('last time is :{}'.format(time.time() - start_time))

# io操作多线程比多进程快
def random_sleep(n):
    time.sleep(n)
    return n
```

#### 更加底层的多进程包
学习底层，生产环境还是推荐conncurrent
```
import os
import time
import multiprocessing

pid = os.fork()
print('fank')
if pid == 0:  #pid=0 child pro
    print('child process {}. parent process {}'.format(os.getpid(), os.getppid()))
else:
    print('parent process {}'.format(pid))

time.sleep(2)

def get_html(n):
    time.sleep(n)
    print('sub progress success')
    return n

if __name__ == '__main__':
    program = multiprocessing.Process(target=get_html, args=(2,))
    program.start()
    program.join()
    print(program.pid) #比线程多个pid属性
    print('main progress end')

    #进程池
    pool = multiprocessing.Pool(multiprocessing.cpu_count())
    result = pool.apply_async(get_html,args=(3,))

    #等待所有任务完成
    pool.close()  #关闭接受新的进程
    pool.join()
    print(result.get())

    #imap  按顺序输出
    for result in pool.imap(get_html, [1,5,3]):
        print('{} sleep success'.format(result))

    #imapunordered  按执行速度输出
    for result in pool.imap_unordered(get_html, [1,5,3]):
        print('{} sleep success'.format(result))
```

### 进程间的通讯

共享全局变量在多进程中不适用

mutiprocessing.queue用于进程间的通讯

进程池中的通讯有3个queue需要区分
1. 系统from queue import Queue不能用于通讯
2. from mutiprocessing import Queue 不能用在进程池通讯
3. from mutiprocessing import Manager().queue 用于进程池pool中的通讯

```
import time
from multiprocessing import Process, Queue, Manager, Pipe

def producer(queue):
    queue.put('a')
    time.sleep(2)

def consumer(queue):
    time.sleep(2)
    data = queue.get()
    print(data)

if __name__ == '__main__':
    queue = Queue(10)   #使用multi的queue
    my_producer = Process(target=producer, args=(queue,))
    my_consumer = Process(target=consumer, args=(queue,))
    my_producer.start()
    my_consumer.start()
    my_producer.join()
    my_consumer.join()

    #manage 用于进程池中的通讯
    queue = Manager().queue(10)

    # pipe 用于两个进程间的通讯
    # 通过pipe实现进程通信,pipe只适用与两个进程
    # 为什么不直接用queue？因为pipe的性能高于queue
    receiver_pipe, send_pipe = Pipe()
    my_producer = Process(target=producer, args=(send_pipe,))
    my_consumer = Process(target=consumer, args=(receiver_pipe,))
    my_producer.start()
    my_consumer.start()
    my_producer.join()
    my_consumer.join()

    # 数据同步，以manager dict为例
    def add_data(p_dict, key, value):
        p_dict[key] = value
    process_dict = Manager().dict()
    first_progress = Process(target=add_data, args(process_dict,'f',25))
    second_progress = Process(target=add_data, args(process_dict,'f2',27))
    first_progress.start()
    second_progress.start()
    first_progress.join()
    second_progress.join()
```

### 协程 （有多个入口的函数，可以暂停的函数，可以向暂停的地方传值）

#### 并发
指在一个时间段内，有几个程序在同一个cpu上运行，但任意时刻只有一个程序在cpu上运行

#### 并行
指任意时刻有多个程序运行在多个cpu上

#### 同步
指代码调用io操作时，必须等待io操作完成才返回的调用方式

#### 异步
指代码调用io操作时，不必等待io操作完成就返回的调用方式（future）

#### 阻塞
指调用函数时，当前线程会被挂起（等待）

#### 非阻塞
调用函数时，当前线程不会被挂起，而是立即返回

C10k问题，1999年被提出的一个挑战。要求在一颗1GHz CPU,2G内存,1gbps带宽，让单台服务器同时为1万个客户提供ftp服务。
如果为每个用户开一个线程，作为低配服务器做不到

#### unix下五种io模型
阻塞式io，在等待io操作时，cpu是空闲的

非阻塞式io，做计算任务或者单向发送请求，不需要等待返回的任务

I/O复用，select,poll,epoll由操作系统提供。
select查看那些socket或文件句柄已经准备好了，select可以同时监听多个socket

信号驱动式I/O,操作系统发起的用的较少

异步I/O(POSIX的aio_系列函数)，
其实很多并发框架都是使用io多路复用，用aio不多,aio与io复用并没有很明显的提升，编码难度比io复用高很多。

### select,poll,epoll

他们都是io多路复用机制。io多路复用就是通过一种机制，一个进程可以监视多个描述符，一旦某个描述符就绪（一般为读写就绪），能够通知程序进行相应的读写操作。但select,poll,epoll本质上都是同步io，因为他们都需要在读写事件就绪后自己负责进行读写，也就是说这个读写过程是阻塞的，而异步io则无需自己负责进行读写，异步io的实现会负责把数据从内核拷贝到用户空间。

#### select
select函数监视的文件描述分3类，分别是writefds、readfds和exceptfds。调用后select函数会阻塞，知道有描述符就绪（数据可读可写，或有except),或者超时（timeout指定等待时间，如果立即返回设定为null）,函数返回。当select函数返回后，可以通过遍历fdset,来找到就绪的描述符。
select目前几乎在所有平台上支持。缺点在于单个进程能够监视的文件描述符的数量存在最大限制，在linux上一般为1024，虽然可以修改，但是会造成效率降低。

#### poll
不同与select使用三个位图来表示三个fdset的方式，poll使用一个pollfd的指针实现。
pollfd没有最大数量限制。select和poll一样需要遍历文件描述符来获取已经就绪的socket，随着描述符数量增长，其效率也会下降。

#### epoll
win不支持，linux2.6内核中提出，是select和poll的增强版。epoll查询使用性能很高的红黑树。
1.epoll并不代表一定比select好，在并发高的情况下，连接活跃度不高，epoll比较好（网站）。
并发性不高，同时连接活跃，select比较好（比如游戏）。

通过非阻塞io实现http请求


#### C10M问题
如何利用8核心cpu，64G内存，在10gbps的网网上保持1000万并发连接

#### 协程解决的问题
主要是解决回调编写难的问题。保持性能+代码编写容易

1.采用同步的方式编写异步的代码
2.使用单线程去切换任务：
    1. 线程是由操作系统切换的，单线程切换意味着需要程序员去调度任务
    2. 不在需要锁，并发性高，如果单线程内切换函数，性能高于线程切换，

#传统函数调用方式 A-B-C,一旦调用其他函数，函数只运行一次然后退出
#我们需要一个可以暂停的函数，并且可以在适当的时候恢复该函数继续执行
#协程  -> 有多个入口的函数，可以暂停的函数，并且可以向暂停的地方传入值
生成器就是可以暂停的函数

### 生成器进阶send,close,throw

send

```
def gen_func():
    #这种写法1.可以产出值 2.可以接受值
    html = yield "http://www.baidu.com"
    print(html)
    yield 2
    yield 3
    return 'fank'

#生成器不仅可以产生值，还可以接受值
if __name__ == '__main__':
    gen = gen_func()
    #启动生成器的方式有2种，1.next() 2.send
    print(next(gen))
    #send默认包含了next
    print(gen.send('fank'))
    print(next(gen))
```

close

```
def gen_func():
    #这种写法1.可以产出值 2.可以接受值
    try:
        yield "http://www.baidu.com"
    except GeneratorExit:
        pass
    yield 2
    yield 3
    return 'fank'

#生成器不仅可以产生值，还可以接受值
if __name__ == '__main__':
    gen = gen_func()
    #启动生成器的方式有2种，1.next() 2.send
    print(next(gen))
    gen.close()
    print(next(gen))

    #GeneratorExit是继承BaseException
```

throw

```
def gen_func():
    #这种写法1.可以产出值 2.可以接受值
    try:
        yield "http://www.baidu.com"
    except GeneratorExit:
        pass
    yield 2
    yield 3
    return 'fank'

#生成器不仅可以产生值，还可以接受值
if __name__ == '__main__':
    gen = gen_func()
    #启动生成器的方式有2种，1.next() 2.send
    print(next(gen))
    gen.throw(Exception, 'download error') #没有向下执行，是当前yeild的异常
```

#### yield from
生成器实现协程是由程序员自己调度的，线程，进程由操作系统内核调度。协程是函数级别的

yield from是python3.3之后新加入的语法

python3.5之后的协程是原生协程，之前是利用生成器完成

```
from itertools import chain

my_list = [1,2,3]
my_dict = {
    'fank':"fankcoder.com",
    'fank1':"fankcoder1.com",
    'fank2':"fankcoder2.com"
}

# yeild from 后面跟一个iterable
# 但远不止这些，如果yeild from 跟生成器
def my_chain(*args, **kwargs):
    for item in args:
        yield from item
        # for each in item:
        #     yield each

for value in my_chain(my_list, my_dict, range(5,10)):
    print(value)

def g1(gen):
    yield from gen

def main():
    g = g1
    g.send(None)

#1. main调用生成器 2.g1委托生成器 3.gen子生成器
#2. yield from 会在调用生成器和子生成器之间建立一个双向通道，
# 两者可以互通，现在调用生成器可以直接发送close,throw到子生成器

```

协程调度，事件循环+协程模式，协程是单线程模式.
编写时候凡是遇到耗io的操作，都用啥yield或yield from模式.
tornado是生成器生成的协程.

#### async和await 原生协程

```
#python3.5以后为了将语义变得更加明确，就引入了async和await关键词来定义原生协程
# async下不能再出现yield,同样await只能出现在async下
# await 后面跟的函数必须是awaitable也就是加了async的函数
import types

async def downloader(url):
    return 'fank'

# 另一种方法让函数变为awaitable，但是我没有试验成功。不过这种方法本来不推荐
@types.coroutine
def downloader2(url):
    return 'fank2'

async def download_url(url):
    # do something
    # await对应生成器的yield from
    html = await downloader2(url)
    return html

if __name__ == "__main__":
    coro = download_url('http://www.google.com')
    # next(coro)  原生协程不能这样调用
    coro.send(None)

```

### asyncio模块
把它叫做异步Io库，并不叫协程库，这里包含了多线程，多进程，协程

1. 包含各种特定系统实现的模块化事件循环
2. 传输和协议抽象
3. 对tcp,udp,ssl,子进程，延时调用以及其他的具体支持
4. 模仿Futures模块但适用于事件循环适用的Future类
5. 基于yield from的协议和任务，可以让你用顺序的方式编写并发代码
6. 必须使用一个将产生阻塞io的调用时，哟接口可以把这个事件转移到线程池
7. 模仿threading模块中的同步原语，可以用在单线程内的协程之间


 asyncio 异步io并发编程 py3.4以后支持

事件循环

协程编码模式3个：1.事件循环 2.回调(驱动生成器) 3.epoll（io多路复用）

应用：tornado, gevent, twisted(scrapy, django channels)

ps: tornado不建议使用Pymysql,mysqlclient

```
#事件循环+回调（驱动生成器）+epoll(IO多路复用)
#asyncio是Python用于解决异步io的一整套解决方案
#tornado,gevent,twisted(scrapy, dango channels(http2.0 websocket) 目前都是基于twisted)
#tornado(实现了web服务器)，django+flask(uwsgi,gunicorn+nginx)
#tornado可以直接部署，外加nginx

#asyncio
import asyncio
import time

async def get_html(url):
    print('start get html')
    #time.sleep()  #不能使用同步阻塞的方法
    await asyncio.sleep(2)  #耗时操作，io操作加await
    print('end get url')

if __name__ == "__main__":
    start_time = time.time()
    loop  = asyncio.get_event_loop()  #事件循环，自动select
    tasks = [get_html('http://www.google.com') for i in range(10)]
    # loop.run_until_complete(get_html('http://www.google.com'))
    loop.run_until_complete(asyncio.wait(tasks))
    print('time:', time.time() - start_time)

#获取协程的返回值
from functools import partial

async def get_html(url):
    print('start get html')
    #time.sleep()  #不能使用同步阻塞的方法
    await asyncio.sleep(2)  #耗时操作，io操作加await
    return 'fank'

def callback(url, future): #partial url 要写在前面
    print(url)

if __name__ == "__main__":
    start_time = time.time()
    loop  = asyncio.get_event_loop()  #事件循环，自动select
    # get_future = asyncio.ensure_future(get_html('http://www.google.com'))  #一个线程只有一个loop，这里ensure_future自动帮我们获取loop
    task = loop.create_task(get_html('http://www.google.com'))  #和上一句等效
    # loop.run_until_complete(get_html('http://www.google.com'))
    # task.add_done_callback(callback)
    #如过callback需要传参
    task.add_done_callback(partial(callback, 'http://www.googl.com'))

    # loop.run_until_complete(get_future)
    loop.run_until_complete(task)
    # print(get_future.result())
    print('time:', time.time() - start_time)

#wait 和gather的用法和区别
#gather更加高层，可以分组
async def get_html(url):
    print('start get html')
    #time.sleep()  #不能使用同步阻塞的方法
    await asyncio.sleep(2)  #耗时操作，io操作加await
    print('end get url')

if __name__ == "__main__":
    start_time = time.time()
    loop  = asyncio.get_event_loop()  #事件循环，自动select
    group1 = [get_html('http://www.google.com') for i in range(10)]
    group2 = [get_html('http://www.google.com') for i in range(10)]
    # loop.run_until_complete(get_html('http://www.google.com'))
    group2.cancel() #批量取消
    loop.run_until_complete(asyncio.gather(*group1, *group2))
    print('time:', time.time() - start_time)
```

#### 取消task

```
# run_until_complete
import asyncio

# loop = asyncio.get_event_loop()
# loop.run_forever()  #不会停止，会一直运行
# loop.run_until_complete()  #运行了指定的协程之后会停止

async def get_html(sleep_times):
    print('waiting')
    await asyncio.sleep(sleep_times)
    print('done after {}'.format(sleep_times))

if __name__ == "__main__":
    task1 = get_html(2)
    task2 = get_html(3)
    task3 = get_html(3)

    tasks = [task1, task2, task3]
    loop = asyncio.get_event_loop()

    try:
        loop.run_until_complete(asyncio.wait(tasks))
    except KeyboardInterrupt as e:
        all_tasks = asyncio.Task.all_tasks()
        for task in all_tasks:
            print('cancel task')
            print(task.cancel())
        loop.stop()
        loop.run_forever()  #很关键，不加会报错
    finally:
        loop.close()
```

#### 底层方法call_soon,call_later,call_at

```
import asyncio

#可以给asyncio直接传递函数（不是async函数）
def callback(sleep_time):
    print('sleep {} success'.format(sleep_time))

def stoploop(loop):
    loop.stop()

if __name__ == "__main__":
    loop = asyncio.get_event_loop()
    loop.call_soon(callback, 2)  #即刻执行，在队列里等到下一个循环立马执行
    loop.call_soon(stoploop, loop)

    loop.call_later(2,callback,2)  #在2秒钟之后运行callback
    loop.call_later(1,callback,1)
    loop.call_later(3,callback,3)

    now = loop.time()  #loop的time
    loop.call_at(now+2, callback, 2)
    loop.call_at(now+3, callback, 3)

    loop.call_soon_threadsafe()  #变量线程安全

    loop.run_forever()
```

#### asyncio+ThreadPollExecutor

```
#使用多线程:在协程中继承阻塞io(某些库就是阻塞的)
import asyncio
import socket
from urllib.parse import urlparse
from concurrent.futures import ThreadPoolExecutor

def get_url(url):
    # get html by socket
    url = urlparse(url)
    host = url.netloc
    path = url.path
    if path == '':
        path = '/'
    
    #connect socket
    client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    client.connect((host, 80))
    client.send("GET {} HTTP/1.1\r\nHOST:{}\r\nConnection:close\r\n\r\n".format(path, host).encode('utf8'))
    
    data = b''
    while True:
        d = client.recv(1024)
        if d:
            data += d
        else:
            break
    data = data.decode('utf8')
    htmldata = data.split('\r\n\r\n')[1]
    print(htmldata)

if __name__ == "__main__":
    loop = asyncio.get_event_loop()
    executor = ThreadPoolExecutor(3)
    tasks = []
    for i in range(20):
        #参数1.线程池 2.函数名 3.函数参数
       task = loop.run_in_executor(executor, get_url, 'http://www.baidu.com/{}'.format(i))  #将某个阻塞Io函数放入executor中运行
       tasks.append(task)
    loop.run_until_complete(asyncio.wait(tasks))
```

#### 同步的方式实现异步http模拟请

```
#asyncio没有提供http协议的接口；aiohttp异步的requests，可以启动服务器，可以爬虫
#同步的方式实现异步http模拟请求
import asyncio
import socket
from urllib.parse import urlparse

async def get_url(url):
    # get html by socket
    url = urlparse(url)
    host = url.netloc
    path = url.path
    if path == '':
        path = '/'
    
    #connect socket
    reader, writer = await asyncio.open_connection(host, 80)  #线程
    writer.write("GET {} HTTP/1.1\r\nHOST:{}\r\nConnection:close\r\n\r\n".format(path, host).encode('utf8'))
    
    all_lines = []
    async for raw_line in reader:  #异步化，因为内部有yield from语法
        data = raw_line.decode('utf8')
        all_lines.append(data)

    html = '\n'.join(all_lines)
    return html

if __name__ == '__main__':
    loop = asyncio.get_event_loop()
    tasks = []
    for i in range(20):
        url = 'http://www.baidu.com/{}'.format(i)
        tasks.append(asyncio.ensure_future(get_url(url)))
    loop.run_until_complete(asyncio.wait(tasks))
    for task in tasks:
        print(task.result()
```

#### asyncio同步和通信

```
import asyncio
from asyncio import Lock,Queue
queue = Queue()
await queue.get()
queue1 = [] #普通的全局变量也能用，但是不能控制Size流量

cache = {}
lock = Lock()

async def get_stuff(url):
    async with lock:
        # await lock.acquire()  #async 
        if url in cache:
            return cache[url]
        stuff = await aiohttp.request('get', url)
        cache[url] = stuff
        return stuff
        # lock.release()  #不需要await

async def parse_stuff():
    stuff = await get_stuff()

async def use_stuff():
    stuff = await get_stuff()
```