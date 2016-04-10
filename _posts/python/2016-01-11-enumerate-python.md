---
layout: post
title: Python黑魔法(split,enumerate)
category: Python
tags: python
description:
---

### split()

```
#!/usr/bin/python
 
str = "Line1-abcdef \nLine2-abc \nLine4-abcd";
print str.split( );
print str.split(' ', 1 );
```

输出结果如下：

```
['Line1-abcdef', 'Line2-abc', 'Line4-abcd']
['Line1-abcdef', '\nLine2-abc \nLine4-abcd']
```

split()要点:

采用不带参数的split()，它会把所有空格（空格符、制表符、换行符）当作分隔符。

filter(None, s.split(' '))

```
a = 'hello++world!+'   #如何取得'hello'和'world'呢？
 
>>>a.split('+')
 
['hello', '', 'world!', '']    #含有两个空字符串
 
>>>filter(None, a.split('+'))
 
['hello', 'world!']
```

### enumerate()

写一个带引索的遍历数组

```
for i in range (0,len(list)):
    print i ,list[i]
```

如果使用enumerate():

```
l = [1,2,3,'a','b']
 
 for index,data in enumerate(l):
     print index,data
 
>>   
0 1
1 2
2 3
3 a
4 b
```

简直太方便了~
