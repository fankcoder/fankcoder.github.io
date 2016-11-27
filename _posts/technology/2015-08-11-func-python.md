---
layout: post
title: Python内置函数(map、reduce、filter)
category: 技术
tags: python
description:
---

### map

map(...)
    map(function, sequence[, sequence, ...]) -> list 说明：

```
对sequence中的item依次执行function(item)，执行结果输出为list。
```

例子：

```
>>> map(str, range(5))           #对range(5)各项进行str操作
['0', '1', '2', '3', '4']        #返回列表
>>> def add(n):return n+n
... 
>>> map(add, range(5))           #对range(5)各项进行add操作
[0, 2, 4, 6, 8]
>>> map(lambda x:x+x,range(5))   #lambda 函数，各项+本身
[0, 2, 4, 6, 8]
>>> map(lambda x:x+1,range(10))  #lambda 函数，各项+1
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

### reduce

reduce(...)
    reduce(function, sequence[, initial]) -> value 说明：

```
对sequence中的item顺序迭代调用function，函数必须要有2个参数。要是有第3个参数，则表示初始值，可以继续调用初始值，返回一个值。
```

例子：

```
>>> def add(x,y):return x+y
... 
>>> reduce(add,range(10))        #1+2+3+...+9
45
>>> reduce(add,range(11))        #1+2+3+...+10
55
>>> reduce(lambda x,y:x*y,range(1,3),5)           #lambda 函数，5是初始值， 1*2*5
10
>>> reduce(lambda x,y:x*y,range(1,6))             #阶乘，1*2*3*4*5
120
>>> reduce(lambda x,y:x*y,range(1,6),3)           #初始值3，结果再*3
360
>>> reduce(lambda x,y:x+y,[1,2,3,4,5,6])          #1+2+3+4+5+6
21 
```

### filter

filter(...)
    filter(function or None, sequence) -> list, tuple, or string 说明：

```
对sequence中的item依次执行function(item)，将执行结果为True（！=0）的item组成一个List/String/Tuple（取决于sequence的类型）返回，False则退出（0），进行过滤。
```

例子：

```
>>> def div(n):return n%2
... 
>>> filter(div,range(5))                    #返回div输出的不等于0的真值
[1, 3]
>>> filter(div,range(10))
[1, 3, 5, 7, 9]
>>> filter(lambda x : x%2,range(10))        #lambda 函数返回奇数，返回列表
[1, 3, 5, 7, 9]
>>> filter(lambda x : not x%2,range(10))
[0, 2, 4, 6, 8]
>>> def fin(n):return n!='f'                #过滤'z' 函数，出现z则返回False
... 
>>> filter(fin,'fankcoder')                    #'z'被过滤
'ankcoder'
>>> filter(lambda x : x !='f','fankcoder')     #labmda返回True值
'ankcoder'
>>> filter(lambda x : not x=='f','fankcoder')  #返回：字符串
'ankcoder'
```
