---
layout: post
title: Python转码问题
category: 技术
tags: python
description:
---


在Python中，可以对String调用decode和encode方法来实现转码。
 
比如，若要将某个String对象s从gbk内码转换为UTF-8，可以如下操作 

```
s.decode('gbk').encode('utf-8′) 
```

可是，在实际开发中，我发现，这种办法经常会出现异常： 

```
UnicodeDecodeError: ‘gbk' codec can't decode bytes in position 30664-30665: illegal multibyte sequence 
```

这是因为遇到了非法字符——尤其是在某些用C/C++编写的程序中，全角空格往往有多种不同的实现方式，比如\xa3\xa0，或者\xa4\x57，这些字符，看起来都是全角空格，但它们并不是“合法”的全角空格（真正的全角空格是\xa1\xa1），因此在转码的过程中出现了异常。 

这样的问题很让人头疼，因为只要字符串中出现了一个非法字符，整个字符串——有时候，就是整篇文章——就都无法转码。 

解决办法： 

```
s.decode('gbk', ‘ignore').encode('utf-8′) 
```

因为decode的函数原型是decode([encoding], [errors='strict'])，可以用第二个参数控制错误处理的策略，默认的参数就是strict，代表遇到非法字符时抛出异常； 
如果设置为ignore，则会忽略非法字符； 
如果设置为replace，则会用?取代非法字符； 
如果设置为xmlcharrefreplace，则使用XML的字符引用。 
