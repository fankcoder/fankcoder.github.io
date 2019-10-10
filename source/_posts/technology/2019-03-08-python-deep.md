---

layout: post

title: Python深入学习笔记

category: 技术

tags: python

keywords: pyhton

---

### collections
```
from collections import *

from collections.abc import * #抽象基类
```


#### tuple功能
1. 不可变(不绝对)
2. iterable

```
#拆包，方便找到对应关系
_tuple = ('fank', 26, 182)
name, age, height = _tuple
#name = _tuple[0] 不好的写法

#拆包加强
name, *other = _tuple
print(name, other)
```

#### tuple比list好的地方
1. immutable的重要性
2. 性能优化，不可变对象在编译时作为常量，产生显著的速度差异
3. 线程安全
4. 可以作为dict的key
5. 拆包特性
6. 如果拿C来类比，tuple对应struct, 而list对应array

#### namedtuple
可以生成类，

    User = namedtuple("User",['name','age'])
    user = User(name='fank', age='27')
    print（user.name, user.age）
    
    _make()使用tuple初始化，_asdict()使用dict初始化
    t = ('fank', 27)
    user = User._make(t)
    d = {'name':'fank','age':27}
    user = User._asdict(d)
    
节省空间，用于数据处理，从数据库取数据为tuple，直接赋值

#### def ask(*args, **kwargs)

*args是个tuple, **kwargs是个dict
```
def test(*args, **kwargs):
    print(*args)
    print(**kwargs)

test('f',25)
test({'name':'f','age':25}

# ask('f',25) 传给*args, ask(name='f', age=25)传给**kwargs
```

#### deque双端队列

应用场景queue是由deque来完成的。deque是线程安全到，list不是线程安全的，由GIL保护。

    from collections import deque

copy是浅拷贝，如果值为list会相互影响

#### counter

统计

    from collections import Counter
    users = ['fank','fank1','fank1','fank2','fank','fank3']
    user_counter = Counter(users)
    print(user_counter)
    #Counter({'fank':2,'fank1':2,'fank3':1})

    #追加统计
    user_counter.update()
    #前n个出现最多的,使用堆实现(完全二叉树)
    user_counter.most_common(n)

#### defaultdict
from collections import defaultdict C语言实现，性能高,比setdefault更强大

```
default_dict = defaultdict(int) #参数是一个可调用对象list,int,func..

default_dict['key'] #key=0 ,初始化成功后，直接访问不会报错，会默认生成

def gen_func():
    return {'fank':123,'code':321}
default_dict = defaultdict(gen_func) #生成默认值
```

#### OrderDict是有序的

dict的子类，按照添加顺序排序。list+dict
py3的dict默认是有序的。

    _d = OrderedDict()
    _d.popitem()  #不需要传key
    _d.move_to_end()


#### ChainMap 

方便操作多个dict. 将dict连结,key相同只有一个。

    from collections import ChainMap

    new_dict = ChainMap(_d1, _d2)
    new_dict.new_child({'name':'fank'})

    new_dict.maps  #maps属性，以列表形式全部打印，同样可以修改
    

### inspect

    import inspect
    inspect.getfile(obj)
    inspect.getsource(obj)
  
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
```
In [46]: a = 1
In [47]: b = 1
In [48]: a is b
Out[48]: True

In [49]: a = [1,2,3]
In [50]: b = [1,2,3]
In [51]: a is b
Out[51]: False

In [72]: a = "hello world"
In [73]: b = "hello world"
In [74]: a is b
Out[74]: False
```


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

### bisect二分查找
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

浅拷贝就是创建一个具有相同类型，相同值（值的id也相同）但不同id的新对象。

深拷贝值为list时，list的id不同。

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

### property

property动态属性，User.age可以取属性，不用调函数，类似get，set为

```
class User:
    def __init__(self, birthday):
        self.birthday = birthday
        self._age = 0
    @propery  #装饰器。讲age变为属性描述符,直接调用
    def age(self):
        return datetime.now().year - self.birthday.year
    @age.setter  #设置
    def age(self, value):
        self._age = value
    
user = User(year='1992')
user.age
user.age = 26  #属性赋值
```

### __getattr__与__getattribute__的区别

__getattr__在查找不到属性的时候调用(写了就不会报错).维护dict动态访问key

def __getattr__(self, item): return "not find"

```
from datetime import date
class User:
    def __init__(self, name,info={}):
        self.name = name
        self.info = info
        
    def __getattr__(self, item):
        return self.info[item]
user = User('fank', info={'a':1})
user.a
```


def __getattribute__(self, item): return "true" 
优先级别高，无条件进入这个函数，它控制全局属性访问

### 属性描述符
在赋值的时候检查属性
实现了__get__,__set__,__delete__三个中的任意一个的类都是属性描述符

```
class IntField:
    def __get__(self, instance):
        return self.value
    def __set__(self, instance,value):
        if isinstance(value, numbers.Interagral):
            raise ValueError('int value need')
        self.value = value
    def __delete__(self, instance):
        pass

class User:
    age = Intfield()
```

数据描述符都实现
非数据描述符只实现__get__

属性查找过程

传递方法为变化的user.age方式无法实现， 使用getattr(user, age)

### __new__和__init__区别

```
class User:
    def __new__(cls, *args, **kwargs): #写框架使用很频繁
        pass
        return super().__new__(cls)
    def __init__(self, name):
        self.name = name
user = User('fank')
```

new传递类cls，在init之前，new控制对象的生成过程.
如果new不返回对象，不会调用init函数

type是默认元类，metaclass是自定义元类，可以控制实例化过程


### 元类编程
类也是对象，type是用来创建类的类

```
def create_class(name):
    if name == "user":
        class User:
            def __str__(self):
                return 'user'
        return User
    elif name == "company":
        class Company:
            def __str__(self):
                return 'Company'
        return Company

#type是可以创建类的。动态创建类
User = type("User", (), {"name":"user"})  #第二个参数继承基类必写
u = User()
print(u.name)

#如何给type写方法
def say(self):
    retun "i am user"
User = type("User", (), {"name":"user", "say":say}) 
u = User()
print(u.say)
#继承
User = type("User", (Baseclass,), {"name":"user", "say":say})
```

元类是创建类的类， 对象<-class<-type

```
class MetaClass(type):
    def __new__(self, *args, **kwargs):
        return super().__new__(cls, *args, **kwargs) #带*参数必传否则会报错

class User(metaclass=MetaClass):  #当不写metaclass会默认调用type创建类对象
    pass
#python类的实例化过程中，会首先寻找metaclass,通过metaclass创建类，控制实例化过程
```

### 迭代器和生成器

迭代器是访问集合内元素的一种方式，一般用来遍历数据。

可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator。实现了`__iter__`和`__next__()`方法的对象都是迭代器。

可以使用isinstance()判断一个对象是否是Iterator对象。

生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator。

python的Iterator对象表示的是一个数据流，Iterator对象可以被next()函数调用并不断返回下一个数据，直到没有数据时抛出StopIteration错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过next()函数实现按需计算下一个数据，所以Iterator的计算是惰性的，只有在需要返回下一个数据时它才会计算。

把list、dict、str等Iterable变成Iterator可以使用iter()函数：

```
>>> isinstance(iter([]), Iterator)
True
>>> isinstance(iter('abc'), Iterator)
True
```

next(itor)接受迭代器，迭代器next无法切片。文件太大使用迭代器读数据

__iter__迭代协议，可迭代的实现方法
```
from collections.abc import Iterable,Iterator

iter([_list]) #将list变为迭代器

def __iter__(self) / def __getitem__(self, item)
```


生成器函数，函数内有yield关键字
```
def gen_func(): 
    yield 1 yield2 yield3
```
python在编译时字节码发现yield于是变为生成器
python查看字节码
```
import dis
print(dis.dis(func))
```
gen = gen_func() #gen生成器对象，不是值

for i in gen: print(i) 惰性求值提供了可能，比list好处是省内存

生成器读取大文件
```
#数据在文件中只有1行
f = open()
f.read(4096) #一次读取的大小，偏移量

def myreadlines(f, newline):
    buf = ""
    while True:
        while newline in buf:
            pos = buf.index(newline)
            yield buf[:pos]
            buf = buf[pos + len(newline):]
        chunk = f.read(4096)
        
        if not chunk:
            yield buf
            break
        buf += chunk
```

### socket编程
服务端socket
1. bind(协议，地址，端口）
2. listen(监听客户端socket请求）
3. accept()
4. 阻塞等待连接请求（新套接字）
5. recv()
6. send()
7. close()

客户端socket
1. connect() 三次握手 -> 等待套接字
2. send() -> recv() server #接受bytes类型需要编码
3. recv() -> send() server
4.close()

爬虫是典型的客户端socket
如果使得服务端可以多用户连接？
将每个socket做为线程
```
import socket

server = socket.socket()

server.bind(('0.0.0.0', 8000))

server.listen()

sock, addr = server.accept()

data = server.recv(1024) #1kb
```

socketHttp
reqeusts -> urlib -> socket
```
import socket
from urllib.parse import urlparse

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

if __name__ == '__main__':
    get_url('http://www.baidu.com')
```

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

#文件读写，写一般只用一个线程，读可以允许有多个线程

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

### aiohttp实现高并发爬虫

#### aiohttp
HTTP client/server framework for asyncio

server可以搭建http服务器

### client爬虫
爬取Url
抽取内部Url
过滤
反爬
分布式

#### sanic
号称可以媲美go性能的高并发web服务器


