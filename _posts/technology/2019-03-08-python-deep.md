---

layout: post

title: Python深入学习笔记

category: 技术

tags: python

keywords: pyhton

---

  
### type,object和class的关系

type(int) -> class type -> obj

type -> int -> 1

type生成类对象，类对象生成实例对象

int.__bases__ class ‘object’

基类均为class object, object是最顶层基类。

type.__bases__ class ‘object’ type继承obj

type(object) class ’type’ obj是type的实例, 形成环路

object.__bases__ None

### 内置类型

对象3个特征:
1. 身份id(a) 
2. 2.类型type 
3. 3.值

None全局唯一

数值int, float,complex复数,bool

迭代类型

序列类型 list,bytes,range,tuple,str,array

映射类型 dict

 集合 set , frozenset

上下文管理器 with

其他 模块from import,class,object,type


### 魔法函数

以双下划线开头，双下划线结尾。可以定制类的特性

__iter__,__getitem__实现了都是可迭代的

def __getitem__(self, item):

return self._list[item]

拆包，给长度数量的变量给他们赋值


### 鸭子类型和多态
当一只鸟，走路，游泳，叫起来都像鸭子，那么这只鸟就可以被叫做鸭子
都实现了同一种方法，所以可以归为一类，不需要继承，只需要实现同方法，就可以实现多态

```
class Cat(object):
    def say(self):
        print(‘im a cat’)

class Dog(object):
    def say(self):
        print(‘im a dog’)


animal = Cat
animal().say()

animal_list = [Cat, Dog]
for animal in animal_list:
    animal().say()
```

### 抽象基类abc模块
检查某个类是否有某种方法

```
class Company(object):
    def __init__(self, employee_list):
        self.employee = employee_list

    def __len__(self):
        return len(self.employee)

com = Company([‘bob1’,’bob2’])
print(hasattr(com, ‘__len__’) #检验类是否具有该方法
# print(len(com))
```

### 抽象基类abc模块
```
from collections.abc import Sized
isinstance(com, Sized) # 判断类型
import abc

class CacheBase(metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def get(self, key):
        pass

    @abc.abstractmethod
    def set(self, key, value):
        pass

class RedisCache(CacheBase):
    pass

redis_cache = REdisCache()  
#会报错。
#abstractmethod要求被继承的类，必须实现父类的方法，否则会报错
```

判断继承用isinstance()不要用type()

#### 类变量和实例变量
类中的self是类的实例
```
class A:
    aa = 1 #类变量
    def __init__(self, x, y):
        self.x = x
        self.y = y  #实例变量
a = A(2,3)
A.aa = 11 #修改类变量
a.aa = 100 #新建变量
print(a.x, a.y, a.aa)
print(A.a)
print(A.x) #error
```
self是类的实例，类变量和类实例变量的区别

  
__mro__查找。DFS深度优先，对菱形继承有问题，BFS广度优先也有覆盖问题，py3用C3算法

  
@staticmethod静态方法，传参和普通函数一样，**不需要写self参数**

```
class Data:
    @staticmethod
    def parse_from_string(date_str):
        year, month, day = tuple(date_str)
        return Date(year, month, day)  #静态方法硬编码，返回值必须与类名相同
```

@classmethod类方法，第一参数是类本身cls，返回值不需要与类同名

```
    @classmethod
    def from_string(cls, date_str):  #cls传递的是类本身，不是实例
        year, month, day = tuple(date_str)
        return cls(year, month, day)  #写为cls,变为动态不必与类名相同
#cls是习惯性写法，在这里甚至可以写为self
```

### 类的私有属性

```
self.__birthday双下划线开头，但并不绝对安全，_User__birthday依然可以访问到
class User:
    def __init__(self):
        self.__birthday = birthday  #无法被访问
    def get_age(self):
        return 2019 - self.__birthday.year
    def __get_other(self):  #私有方法
        pass
user = User(Date(1992,9,28))
user._User__birthday  #py帮助我们使用这种简单方式封装，找到规律就可以访问
```

### 自省机制
自省是通过一定的机构查询到对象的内部结构

```
class Person:
    name = ‘user’

clss Student(Person):
    def __init__(self, school_name):
        self.school_name = ‘'
user = Student(‘abc’)
print(user.__dict__)  #打印实例属性，{’school_name’:abc}.c优化的魔法函数
print(Person.__dict__) #类属性
user.__dict__[’school_addr’] = ‘efg’  #甚至可以赋值
dir(user)  #更详细，功能更强
```

### super
获取父类init (其实是mro顺序的类，查看方法classname.__mro__)
```
class A:
    def __init__(self):
        print(‘a’)

class B(A):
    def __init__(self):
        print(‘b’)
        super(b, self).__init__() # py2
        super().__init__() # py3

b = B()

from threading import Thread
class MyThread(Thread):
    def __init__(self, name, user):
        self.user = user
        super().__init__(name=name)  #重用父类代码
```
### mixin模式
多继承不推荐，mixin代替多继承
特点：
1. Mixin类功能单一
2. 不和基类关联，可以和任意基类组合
3. 在mixin中不要使用supper

### with上下文管理器
```
try:
    raise KeyError
except KeyError as e:
    print('e')
else:
    print('else') #当try正确运行时&except没有捕获到异常打印
finally:
    print('f') #始终运行
```
with语句是为了简化try..finally诞生的
```
class Sample:
    def __enter__(self):
        #获取资源
        print('enter')
        return self
    def __exit__(self, exc_type, exc_val, exc_tb):
        #释放资源
        print('exit')
    def do_something(self):
        print('do smt')

with Sample() as sample:
    sample.do_something() 
```
这个类实现了上下文管理器协议，此协议非常好用。
with是对上下文管理器的支持。

### contextlib简化上下文管理器
进一步简化
```
import contextlib

@contextlib.contextmanager
def file_open(file_name):
    print('file open')  # __enter__的位置
    yield {}
    print('file end')  # __exit__的位置
    
with file_open('text') as f:
    pass

```


### collections
```
from collections import *

from collections.abc import * #抽象基类
```
tuple不可修改并不绝对。tuple值为list可以修改(id没变，不建议这样使用)

tuple比list好处，c语音类比，tuple对应struct,list对应array.性能优化，线程安全，可以作为dict的key,拆包特性。

namedtuple可以生成类，User = namedtuple("User",['name','age'])

节省空间，用于数据处理，从数据库取数据为tuple，直接赋值


### def ask(*args, **kwargs):pass

ask('f',25) 传给*args, ask(name='f', age=25)传给**kwargs

*args是个tuple, **kwargs是个dict


_make方法初始化，_asdict()

  

from collections import defaultdict C语言实现，性能高,比setdefault更强大

default_dict = defaultdict(int) 参数是可调用对象

default_dict['key'] #key=0

  

### deque双端队列

应用场景queue是由deque来完成的。deque是线程安全到，list不是线程安全的，由GIL保护。

from collections import deque

copy是浅拷贝，如果值为list会相互影响

  

counter(iterm) 快速统计,counter.update().most_common(n)出现最多到前n个,使用堆实现(完全二叉树)

  

### OrderDict是有序的

py3的dict默认是有序的。

_d = OrderedDict() _d.popitem()不需要传key _d.move_to_end()


### ChainMap 

方便操作多个dict. 将dict连结,key相同只有一个。

from collections import ChainMap

new_dict = ChainMap(_d1, _d2)

new_dict.maps全部打印，同样可以修改

  

### 类和对象

type() object

魔法函数

hasattr(param,"__len__") 判断是否有这个函数，用isinstance()更好

希望用户继承时必须实现方法

class CacheBase():

def get(self, key):raise NotImplementedError

def set(self, key, value):pass

  

### is 和 ==

is判断id是否相同

==判断值

isinstance 和type，判断类的类型尽量用isinstance



### 自定义序列类

容器序列，扁平序列，可变序列sequence，不可变序列mutable

python面向协议编程 in可以作用于list

### +,+=,extend
+=（__iadd__）的参数可以是序列，+必须两边同类型
a.extend(range(0,3)) #直接对值修改，不需要再赋值，参考apped
a.append([1,2])  #[1,2,[1,2]]注意和extend的区别


参数可以为任意序列，通过for循环append

切片[start : end : step]，默认值可省略[0,len(L),1].

实现支持切片操作的类
```
class Group:
    def __init__(self, group_name, company_name, staffs):
        self.group_name = group_name
        self.company_name = company_name
        self.staffs = staffs
    
    def __reversed__(self):
        self.staffs.reversed()  #这种方式实现不了
        for each in self.staffs[::-1]:
            yield each
        
    # def __getitem__(self, item):
    #     return self.staff[item] #返回对象为list或单个元素
    import numbers
    def __getitem__(self, item):  #返回类对象
        cls = type(self)
        if isinstance(item, slice):
            return cls(group_name=self.group_name, company_name=self.company_name, staffs=self.staffs[item])
        elif isinstance(item, numbers.Interal):
            return cls(group_name=self.group_name, company_name=self.company_name, [staffs=self.staffs[item]])
    
    def __len__(self):
        return len(self.staffs)
        
    def __iter__(self):
        return iter(self.staffs)
    
    def __contains__(self, item):
        if item in self.staffs:
            return True
        else:
            return False
        
staffs = ['a', 'b', 'c']
group = Group(group_name='gname', company_name='cname', staff=staffs)
group[:2]
for user in group:
    print(user)
```

### bisect
bisect处理已排序序列，二分查找。插入排序好的数列，升序bisect.insort(list, 5).
二分法查找位置，插入位置bisect.bisect(list, 3)
```
import bisect

l = []
bisect.insort(4)
bisect.insort(1)
bisect.insort(3)
bisect.insort(2)
print(l) #[1,2,3,4]
```

arrary和list重要区别，array只能存放指定的数据类型。布隆过滤器。
arrary是c中的数组，一串连续的内存块，性能高。
```
import array
_a = array.array('i') # int,参数查看文档
_a.append()
```


### 列表推导式（生成式）&生成器表达式
通过一行代码生成列表。

列表生成式性能能高于列表操作
```
odd_list = [i for i in range(21) if i%2 == 1]
```

复杂情况的列表生成式，可以使用函数实现.（过于复杂就放弃吧，代码可读性更重要）
```
def sqrt(item):
    return item*item
odd_list = [sqrt(i) for i in range(21) if i%2 == 1]
```


生成器表达式为小括号，type变为generator
```
odd_list = (i for i in range(21) if i%2 == 1)
```

字典推导式
```
my_dict ={'a':1, 'b':2}
_reversed = {value:key for key, value in my_dict.items()} # 交换value,key
```

集合推导式
```
my_set = {key for key,value in my_dict.items()}
type(my_set)  # set
```

### 深入set和dict
```
a.copy() #浅拷贝

import copy 

copy.deepcopy(a) #深拷贝
```
浅拷贝就是创建一个具有相同类型，相同值但不同id的新对象。
深拷贝不仅仅拷贝了原始对象自身，也对其包含的值进行拷贝，它会递归的查找对象中包含的其他对象的引用，来完成更深层次拷贝。因此，深拷贝产生的副本可以随意修改而不需要担心会引起原始值的改变。

#### dict的formkeys方法
```
l = ['a', 'b']
d = dict.fromkeys(l, {'c','d'})
print(d) #{'a': {'c', 'd'}, 'b': {'c', 'd'}}
```
#### get方法
```
_d['key']  #key不存在会抛异常
_dict.get("a", {}) #get方法非常实用！{}为默认值
```

#### items
```
for key, value in _d.items()：
    print(k,v)   #烂大街的好方法
```
#### setdefault 增加get方法
```
setdefault(key, default)
# 1.先调用get()
# 2.没有则默认值
# 3.再把默认值设置进dict
```
#### update
合并字典
```
_dict.update({'c':1})
_dict.update("a"=1,"b"=2) #放iterm都可以
_dict.update(（（’e‘,'f'），）) #传入tuple
```

### dict子类
写类不建议继承list和dict
某些时候c不会使用覆盖的方法，而是会用dict本身的方法
```
from collections import UserDict 
#推荐使用UserDict被python重写过
class Mydict(UserDict):
    def __setitem__(self, key, value):
        super().__setitem__(key, value*2)

from collections import defaultdict

# 如果找不到key,就赋值
my_dict = defaultdict(dict())
my_value = my_dict['fank'] #这里我的py3.7.1报错了
```

### set fronzenset

set和fronzenset(不可变)无序，不重复，在去重时用的很多，并且fronzenset是不可变类型，所以可以做为dict的key
```
s = set('abcd')
s1 = {'a', 'b'} #type是set
s2 = frozenset('abcde') #无法add值，
```

#### set difference
```
s = {'a','b','c'}
ans = set('cef')
rs = s.difference(ans)
print(rs) # {'a','b'}
```

#### dict和set实现原理


dict 查哈希表，key必须为可哈希的值。str,fset,tuple,自己实现的类

dict的内存花销大，查询速度快

垃圾回收算法是采用引用计数 del

### 元类编程

property动态属性，User.age可以取属性，不用调函数，类似get，set为@age.setter

@propery

def age(self):

return age.year

### __getattr__与__getattribute__的区别

__getattr__在查找不到属性的时候调用(写了就不会报错).维护dict动态访问key

def __getattr__(self, item): return "not find"

def __getattribute__(self, item): return "true" 无条件进入这个函数，控制全局属性访问

属性描述符实现__get__,__set__,__delete__

数据描述符，非数据描述符

属性查找过程

传递方法为变化的user.age方式无法实现， 使用getattr(user, age)

### __new__和__init__区别

def __new__(cls, *args, **kwargs):pass new传递类，在init之前，new控制对象的生成过程，如果new不返回对象，不会调用init函数

type是默认元类，metaclass是自定义元类，可以控制实例化过程


### 迭代器和生成器

迭代器是访问集合内元素的一种方式，一般用来遍历数据。

迭代器和以下标的访问方式不一样，迭代器是不能返回的，迭代器提供一种惰性方式。

__iter__迭代协议，可迭代的实现方法

from collections.abc import Iterable,Iterator

iter([_list])迭代器

def __iter__(self) / def __getitem__(self, item)

next(itor)接受迭代器，迭代器next无法切片。文件太大使用迭代器读数据

  

生成器函数，函数内有yield关键字

def gen_func(): yield 1 yield2 yield3

gen = gen_func() #gen生成器对象，不是值

for i in gen: print(i) 惰性求值提供了可能，比list好处是省内存

查看字节码，import dis dis.dis(func)

  

### socket编程

import socket

server = socket.socket()

server.bind(('0.0.0.0', 8000))

server.listen()

sock, addr = server.accept()

data = server.recv(1024) #1kb

  

### 多线、进程

global interpreter lock GIL

python中一个线程对应于c中的线程

gil使得同一时刻只有一个线程在一个cpu上执行字节码

为了安全，但速度慢，无法体现多核优势。pypy是去gil化的。gil在遇到io操作会主动释放

  

操作系统调度的最小单元线程，对于io操作，多线程和多进程差别不大。

1.通过thread类实例化

thread1 = threading.Thread(target=func, args=("",))

thread1.setDaemon(True)#设置为守护线程。当主线程关闭时，子线程被kill

thread1.join() #阻塞，等待执行完

2. 通过继承thread来实现多线程

class GetDetailHtml(threading.Thread):

def run(self):

pass

thread1 = GDH()

thread1.start()

  

### 线程之间的通信方式

1.共享全局变量(list)（多线程可以，多进程不行）

这种方法，线程并不安全，需要加gil锁，所以并不推荐用作通信

2.通过queue方法进行线程间同步,queue本身是线程安全的

from queue import Queue

detail_url_queue = Queue(maxsize=1000)

url = queue.get()#阻塞方法，如果为空就一直停顿等待

queueq.put_nowait()#设置为非阻塞

.join()一直阻塞，想退出需要调用.task_down() #爬虫暂停

如果共用变量为dict或其他，考虑使用全局变量

  

### 线程同步（lock, rlock）

from threading import Lock

lock = Lock()

global lock

lock.acquire()

a +=1

lock.release()

1. 用锁会影响性能

2.锁会引起死锁（互相等待）

rlock可重入的锁，在同一个线程里面，可以连续调用多次acquire，需要相等数量的release.

  

### condition条件变量，用于复杂的线程间同步锁

from threading import Condition

self.cond.acquire()

print()

self.cond.notify()

self.cond.wait()

self.release()

with self.cond = acquire()/release()

在调用with之后，才能使用notify, wait

condition有两层锁，一把底层锁会在线程调用wait方法的时候释放，上面的锁会值每次调用wait时分配一把并放入到cond的等待队列中，等到notify方法唤醒

  

### Semaphore 是用于控制进入数量的锁

#文件读写，写一搬只用一个线程，读可以允许有多个

sem = threading.Semaphore(3)

self.sem.acquire()

self.sem.release() #在另一个函数release，每次3个线程

  

线程池concurrent #py3.2

from concurrent import futures

1.主线程中可以获取某一个线程的状态或者某一个任务的状态，以及返回值

2.当一个线程完成的时候，主线程能立即知道

3.futures可以让多线程多进程编码接口一致

from concurrent.futures import ThreadPoolExecutor

executor = ThreadPoolExecutor(max_workers=2)

task1 = executor.submit(get_html, (3))

print(task1.done()) #done用于判定某个任务是否完成

print(task1.result()) #阻塞的方法，可以获取执行结果

task2.cancel()#取消

  

#获取已经成功的task返回

urls = [3,2,3]

from concurrent.futures import as_completed

all_task = [executor.submit(get_html, (url)) for url in urls]

for future in as_complete(all_task):

data = future.result()

  

简化版,但返回顺序是urls，并不是谁先完成返回谁

for data in executor.map(get_html, urls):

print(data)

  

### 多进程

线程由于有gil无法并发，io操作进程切换代价高于线程。

耗cpu的操作，多核cpu，多进程优于多线程。

进程数据完全隔离,无法使用共享全局变量

子进程完全拷贝Fork之后的父进程代码运行

  

from concurrent.futures import ProcessPoolExecutor

import multiproessing #比上面更底层，但不推荐

def get_html(n):

time.sleep(n)

return n

if __name__ == '__main__':

progress = multiprocessing.Process(target=get_html, args=(2,))

progress.start()

progress.join()

  

queue不能直接用于进程池通信，pool中的通信要使用manager.queue

通过pipe实现进程通信

recevie_pipe, send_pipe = Pipe()

#pipe只适用与两个进程

  

### 协程 （有多个入口的函数，可以暂停的函数，可以向暂停的地方传值）

python3.5之后的协程是原生协程，之前是利用生成器完成

生成器不只可以产出值，还可以接受值send()

send()是协程的基础

throw()扔异常

yield from iterable

```

from itertools import chain

for value in chain(list, dict, range(5))

def my_chain(*args, **kwaags):

for my_iterable in args:

yield from iterable

# for value in my_iterable:

# yield value

  ```

### 原生协程 async和await e sing k

 asyncio 异步io并发编程 py3.4以后支持

事件循环

协程编码模式3个：1.事件循环 2.回调(驱动生成器) 3.epoll（io多路复用）

应用：tornado, gevent, twisted(scrapy, django channels)

ps: tornado不建议使用Pymysql,mysqlclient

获取协程的返回值

```
import asyncio

loop = asyncio.get_event_loop()

loop.run_forever()

loop.run_until_complate()
```
1.loop会放到future中

2.取消future(task)
```
async def get_html():

await asyncio.sleep(sleep_times)
```