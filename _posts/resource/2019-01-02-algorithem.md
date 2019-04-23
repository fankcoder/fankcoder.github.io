---
layout: post
title: 算法&数据结构
category: 备忘
tags: algorithem
keywords: algorithem
description: 
---


## Incremental Method
「递增法」是符合电脑运作特性的方法。电脑执行程式，一次只做一个动作，完成了一件事才做下一件事。当一个问题太大太多时，化整为零、一个一个解决吧！

#### selection sort

      1 def selection(_l):
      2     for i in range(len(_l)):
      3         min = i
      4         for j in range(i+1,len(_l)):
      5             if _l[j] < _l[min]:
      6                 min = j
      7         _l[i], _l[min] = _l[min], _l[i]
      8     print(_l)
      
## Memoization
「记忆法」是符合电脑运作特性的方法。电脑拥有大量储存空间。只要将计算过的数值，储存于记忆体，往后就能直接使用记忆体储存的资料，不必再浪费时间重复计算一遍。

    array = [[0 for i in range(n)] for j in range(n)]
    #利用列表生成式快速创建多维数组
   
## Enumeration
「枚举法」利用了电脑无与伦比的计算速度。找到不确定的变数，枚举所有可能性，逐一判断正确性。

      1 S = "It's a pencil."
      2 s = "a pen"
      3
      4 for i in range(0, len(S)):
      5     flag = True
      6     for j in range(0, len(s)):
      7         if S[i+j] != s[j]:
      8             flag = False
      9     if flag:
     10         print(i, S[i])
     11         break

## Straightforward Method / Trial and Error
「直接法」，直接算出答案。直接法和试误法刚好相反。直接法是由题目本身下手，推导答案；试误法则是从答案下手，让答案迎合题目需求。

#### 暴力攻击（ Brute Force Attack ）

破解密码最简单的方法叫做「暴力攻击」。不知道密码规律的情况下，无法直接推导正确密码，只好以试误法一一检验所有可能的密码，从中找出正确密码。

#### 单向函数（ One-way Function ）
「单向函数」是一种特别的函数，给定输入很容易算出输出，但是给定输出却很难算出输入。

## Iterative Method

叠代法，也叫递推法。不断利用目前求得的数值，再求得新数值。

#### 数学归纳法（ Mathematical Induction ）

    数学归纳法的第二步骤，就是证明可不可以递推！第二步骤的证明过程中一定会用到递推！
    1. 先证明n = 1 成立。（有时候不见得要从1开始。）
    2. 假设n = k 成立，证明n = k+1 也会成立。
    当1. 2. 得证，就表示n = 1 ... ∞ 全部都成立。
    
#### 插入排序法（ Insertion Sort ）

    从表面上来看是递增法与枚举法：第一层是递增法，逐一把每个数字插入到左方已排序的阵列。第二层是枚举法，搜寻插入位置；再将大量数字往右挪，以腾出空间插入数字。
    
    
## Recursive Method
递归法。重复运用相同手法，缩减问题范围，直到厘清细节。
递推法与递归法恰好颠倒：递推法是针对已知，逐步累积，直至周全；递归法是针对未知，反覆拆解，直至精确。

## Greedy Method
「贪心法」。以Incremental Method 或Iterative Method 制造答案的方法，大致上分为两类：

    一、填答案：从没有答案开始，逐步填满答案。
    二、改答案：先随便弄个答案，逐步修饰答案。
    一、观察问题特征，拟定一个填答案、改答案的原则。
    二、随便挑几个特例，手算一下。
      如果答案都对，就大胆假设此原则是正确的。
      （也可以尝试证明此原则必定正确。）
    三、实作程式码，把答案算出来。
    
#### 不重复组合（ Combination ）
    从N 个人抓M 个人出来组团，有哪些组合方式呢？
    N 个人当中的其中一个人，叫做甲君好了，我们将原问题分割成两种情形：甲君在团中、甲君不在团中。甲君在团中，演变成剩下N-1个人要再抓M-1个人出来组团。
    甲君不在团中，演变成剩下N-1个人仍要抓M个人出来组团。

    综合这两个子问题的组合方式，就得到答案。从N 个人抓M 个人出来组团，有哪些组合方式呢？
    N 个人当中的其中一个人，叫做甲君好了，我们将原问题分割成两种情形：甲君在团中、甲君不在团中。甲君在团中，演变成剩下N-1个人要再抓M-1个人出来组团。
    甲君不在团中，演变成剩下N-1个人仍要抓M个人出来组团。

    综合这两个子问题的组合方式，就得到答案。

#### 二分搜寻法（ Binary Search ）
这是在已排序阵列里面搜寻数字的方法。阵列由中央切成两边，一边数字较小、一边数字较大。这两边一定有一边不是我们所要的，可以去除，只需要继续寻找其中一边。

# 数据结构

## Data

#### Array
「数组」。连续的记忆体。搜寻、插入、删除的时间复杂度都是O(N) 。资料已排序，则支援二分搜寻。

#### List （ Linked List ）
「链表」。藉由指标得到下一块记忆体。

搜寻的时间复杂度是O(N) 。知道正确位置，插入与删除的时间复杂度是O(1) ，否则必须先搜寻。无索引值，故不支援二分搜寻。

#### 特殊的List
尾串到头，头尾循环，称作Circular List 。特色是开头可以随便选、随便动。
只串单向，称作Singly Linked List 。双向都串，称作Doubly Linked List ，特色是双向都能搜寻。

#### List 里面放入Array
    英文网路称做Unrolled Linked List ，中文网路称作「松散链表」、「块状链表」。查无正式学术名称。

    N 笔资料，分成A 块，每块约B = N/A 个元素。每块各自记录元素数量。

    索引：先数块、再数元素，时间复杂度为O(A) 。

    搜寻：全找，时间复杂度为O(N) 。资料已排序，则为O(A + logB) 。

    插入、删除：一块大于等于2B 就拆开成两块，相邻两块小于等于B 就合并成一块，避免一拆开就要合并、一合并就要拆开，时间复杂度为O(A + 2B) 到O(2A + B) 。
    
#### Array 里面放入List
大致上就是图论的Adjacency Lists 。

大致上就是之后提到的Hash Table 。

#### Queue
「队列」像排队，维持资料前后顺序。插入、删除需时O(1) 。搜寻需时O(N) 。
FILO

#### Stack
「栈」。像叠盘子，颠倒资料前后顺序。
插入、删除需时O(1) 。搜寻需时O(N) 。
FIFO

#### Deque （ Double Ended Queue ）
两头皆能插入与删除，称作Deque ，同时有着Stack 和Queue 的功效。

## Set
    set 是指数学名词「集合」。在这里我们只考虑元素为整数的集合。集合有几点特性：

    一、空集合。

    二、集合中的元素不会重复。

#### Hash Table
    当元素的数值范围很大，甚至元素不是整数，此时可以利用hash function 得到一个索引值，而不会超出阵列边界。

    数值范围小，索引储存是首选，省时间费空间；数值范围大，循序储存是首选，省空间费时间。hash table 两者兼具，介于中间。


## Sort

| | best case average case worst case | extra space | stable |
--------------- | ---------------------------- | -------- | --------
brute force | O(NR) O(NR) O(NR) | O(N) | O
selection sort | O(NN) O(NN) O(NN) | O(1) | X
bubble sort | O(N) O(NN) O(NN) | O(1) | O
gnome sort | O(N) O(NN) O(NN) | O(1) | O
insertion sort | O(N) O(NN) O(NN) | O(1) | O
Shell sort | O(NN) O(NN) O(NN) | O(1) | X
merge sort | O(NlogN) O(NlogN) O(NlogN) | O(N) | O
Timsort | O(NlogN) O(NlogN) O(NlogN) | O(N) | O
quicksort | O(NlogN) O(NlogN) O(NN) | O(N) | X
heapsort | O(NlogN) O(NlogN) O(NlogN) | O(1) | X
introsort | O(NlogN) O(NlogN) O(NlogN) | O(N) | X
counting sort | O(N+R) O(N+R) O(N+R) | O(N+R) | O
radix sort | O(NlogR) O(NlogR) O(NlogR) | O(N) | O
bucket sort | O(N+R) O(N+R) O(NNR) | O(NR) | X
flashsort | O(N+R) O(N+R) O(NN) | O(N) | X
sleep sort | O(N+R) O(N+R) O(N+R) | O(N) | X

#### 排序原理
    排序的基本原理，当今只有两种，一是对调（数字是实数），二是放置（数字必须是整数）。

    纯粹透过对调来排序，已证明出数字两两比较的次数是Ω(NlogN) ，不可能更少了，当今也已经有了到达下限的排序演算法，例如merge sort 。同时透过对调与放置来排序，则可以打破方才的下限，例如flashsort 。

    纯粹透过放置来排序，需要额外的记忆体空间来放置数字。时间复杂度通常是数字数量加上记忆体用量，效率相当好，只可惜只能处理整数，例如counting sort 。
    
#### selection sort
扫描一遍所有数字，找到最小值，挪至阵列左端。递回处理尚未排序的N-1 个元素。

      1 def selection(_l):
      2     for i in range(len(_l)):
      3         min = i
      4         for j in range(i+1,len(_l)):
      5             if _l[j] < _l[min]:
      6                 min = j
      7         _l[i], _l[min] = _l[min], _l[i]
      8     print(_l)
      
#### Bubble Sort
由左到右，相邻两两比较，较大者往右挪，最后最大值会出现在阵列右端。递回处理尚未排序的N-1 个元素。

      1 def bubble(l):
      2     for i in range(0, len(l)):
      3         for j in range(0, len(l)-i-1):
      4             if l[j] > l[j+1]:
      5                 l[j], l[j+1] = l[j+1], l[j]
      6     print(l)

#### Gnome Sort
原理和Bubble Sort 相同，但是两两比较的先后次序有所改变。特色是程式码只有一个loop，结构简单。

      1 def gnome(l):
      2     i = 0
      3     while i < len(l):
      4         if i == 0 or l[i-1] < l[i]:
      5             i += 1
      6         else:
      7             l[i], l[i-1] = l[i-1], l[i]
      8             i -= 1
      9     print(l)
      
#### Insertion Sort
由左到右，逐一把数字插入到目前已排序的阵列当中。需将大量数字往右挪，以腾出空间插入数字。

      1 def insert(l):
      2     for i in range(1, len(l)):
      3         key = l[i]
      4         j = i - 1
      5         while j >=0 and key < l[j]:
      6             l[j+1] = l[j]
      7             j -= 1
      8         l[j+1] = key
      9     print(l)