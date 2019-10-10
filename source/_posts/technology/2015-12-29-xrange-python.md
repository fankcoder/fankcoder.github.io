---
layout: post
title: Python的xrange和range
category: 技术
tags: python
description:
---

这两个函数基本上都是在循环的时候用。 

```
for i in range(0, 100): 
print i 

for i in xrange(0, 100): 
print i 
```

这两个输出的结果都是一样的，实际上有很多不同，range会直接生成一个list对象： 

```
a = range(0,100) 
print type(a)  #list
print a #[0,1,2...]
print a[0], a[1] 
```

而xrange则不会直接生成一个list，而是每次调用返回其中的一个值 

```
a = xrange(0,100) 
print type(a) #<type 'xrange'>
print a  #xrange(100)
print a[0], a[1] 
```

所以xrange做循环的性能比range好，尤其是返回列表很长的时候！ 
