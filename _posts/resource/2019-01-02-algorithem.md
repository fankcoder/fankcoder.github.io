---
layout: post
title: 算法&数据结构
category: 备忘
tags: algorithem
keywords: algorithem
description: 
---
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

## Binary Tree - 二叉树
二叉树是每个节点最多有两个子树的树结构，子树有左右之分，二叉树常被用于实现二叉查找树和二叉堆。

#### 遍历

    class TreeNode:
        def __init__(self, val):
            self.val = val
            self.left, self.right = None, None

    class Traversal(object):
        def __init__(self):
            self.traverse_path = list()

        def preorder(self, root):
            if root:
                self.traverse_path.append(root.val)
                self.preorder(root.left)
                self.preorder(root.right)

        def inorder(self,root):
            if root:
                self.inorder(root.left)
                self.traverse_path.append(root.val)
                self.inorder(root.right)

        def postorder(self,root):
            if root:
                self.postorder(root.left)
                self.postorder(root.right)
                self.traverse_path.append(root.val)

#### 二叉查找树

一颗二叉查找树(BST)是一颗二叉树，其中每个节点都含有一个可进行比较的键及相应的值，且每个节点的键都大于等于左子树中的任意节点的键，而小于右子树中的任意节点的键。

使用中序遍历可得到有序数组，这是二叉查找树的又一个重要特征。


## Queue
「队列」像排队，维持资料前后顺序。插入、删除需时O(1) 。搜寻需时O(N) 。
Queue 是一个 FIFO（先进先出）的数据结构，并发中使用较多，可以安全地将对象从一个任务传给另一个任务。

#### Deque （ Double Ended Queue ）
两头皆能插入与删除，称作Deque ，同时有着Stack 和Queue 的功效。

    dq = collections.deque();
    dq.appendleft(e)
    dq.append(e)
    dq.popleft()
    dq.pop()

## Heap - 堆

一般情况下，堆通常指的是二叉堆，二叉堆是一个近似完全二叉树的数据结构，但由于对二叉树平衡及插入/删除操作较为麻烦，二叉堆实际上使用数组来实现。即物理结构为数组，逻辑结构为完全二叉树。子结点的键值或索引总是小于（或者大于）它的父节点，且每个节点的左右子树又是一个二叉堆(大根堆或者小根堆)。根节点最大的堆叫做最大堆或大根堆，根节点最小的堆叫做最小堆或小根堆。常被用作实现优先队列。

#### 特点

1. 以数组表示，但是以完全二叉树的方式理解。
2. 唯一能够同时最优地利用空间和时间的方法——最坏情况下也能保证使用 2N \log N2NlogN 次比较和恒定的额外空间。
3. 在索引从0开始的数组中：
父节点 i 的左子节点在位置(2*i+1)
父节点 i 的右子节点在位置(2*i+2)
子节点 i 的父节点在位置floor((i-1)/2)

    class MaxHeap:
        def __init__(self, array=None):
            if array:
                self.heap = self._max_heapify(array)
            else:
                self.heap = []

        def _sink(self, array, i):
            # move node down the tree
            left, right = 2 * i + 1, 2 * i + 2
            max_index = i
            # should compare two chidren then determine which one to swap with
            if left < len(array) and right < len(array):
                flag = array[left] > array[right]
            else:
                flag = True
            if left < len(array) and array[left] > array[max_index] and flag:
                max_index = left
            if right < len(array) and array[right] > array[max_index] and not flag:
                max_index = right
            if max_index != i:
                array[i], array[max_index] = array[max_index], array[i]
                self._sink(array, max_index)

        def _swim(self, array, i):
            # move node up the tree
            if i == 0:
                return
            father = (i - 1) / 2
            if array[father] < array[i]:
                array[father], array[i] = array[i], array[father]
                self._swim(array, father)

        def _max_heapify(self, array):
            for i in xrange(len(array) / 2, -1, -1):
                self._sink(array, i)
            return array

        def push(self, item):
            self.heap.append(item)
            self._swim(self.heap, len(self.heap) - 1)

        def pop(self):
            self.heap[0], self.heap[-1] = self.heap[-1], self.heap[0]
            item = self.heap.pop()
            self._sink(self.heap, 0)
            return item

## Stack
栈是一种 LIFO(Last In First Out) 的数据结构，常用方法有添加元素，取栈顶元素，弹出栈顶元素，判断栈是否为空。

    stack = []
    len(stack) # size of stack

    # more efficient stack
    import collections
    stack = collections.deque()

    len(stack) != 0 - 判断stack是否weikong
    stack[-1] - 取栈顶元素，不移除
    pop() - 移除栈顶元素并返回该元素
    append(item) - 向栈顶添加元素
    
## Set
    Set 是一种用于保存不重复元素的数据结构。常被用作测试归属性，故其查找的性能十分重要

## Map
Map 是一种关联数组的数据结构，也常被称为字典或键值对。在 Python 中 dict(Map) 是一种基本的数据结构。

#### Hash Table
    当元素的数值范围很大，甚至元素不是整数，此时可以利用hash function 得到一个索引值，而不会超出阵列边界。

    数值范围小，索引储存是首选，省时间费空间；数值范围大，循序储存是首选，省空间费时间。hash table 两者兼具，介于中间。

## Graph - 图
图的表示通常使用邻接矩阵和邻接表，前者易实现但是对于稀疏矩阵会浪费较多空间，后者使用链表的方式存储信息但是对于图搜索时间复杂度较高。

#### 邻接矩阵

设顶点个数为 V, 那么邻接矩阵可以使用 V × V 的二维数组来表示。 g[i][j]表示顶点i和顶点j的关系，对于无向图可以使用0/1表示是否有连接，对于带权图则需要使用INF来区分。有重边时保存边数或者权值最大/小的边即可。

#### 邻接表

邻接表通过表示从顶点i出发到其他所有可能能到的边。

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
扫描一遍所有数字，找到最小值，挪至阵列左端。递回处理尚未排序的N-1 个元素。复杂度: O(N2) — 实际上，这和冒泡排序很像。

      1 def selection(_l):
      2     for i in range(len(_l)):
      3         min = i
      4         for j in range(i+1,len(_l)):
      5             if _l[j] < _l[min]:
      6                 min = j
      7         _l[i], _l[min] = _l[min], _l[i]
      8     print(_l)
      
#### Bubble Sort
由左到右，相邻两两比较，较大者往右挪，最后最大值会出现在阵列右端。递回处理尚未排序的N-1 个元素。冒泡排序实际上是低效的，它的 O(N^2) 时间复杂度。

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
由左到右，逐一把数字插入到目前已排序的阵列当中。需将大量数字往右挪，以腾出空间插入数字。最佳情况时间是O(N × 1) = O(N) ，最坏情况时间是O(N × N) = O(N2).

      1 def insert(l):
      2     for i in range(1, len(l)):
      3         key = l[i]
      4         j = i - 1
      5         while j >=0 and key < l[j]:
      6             l[j+1] = l[j]
      7             j -= 1
      8         l[j+1] = key
      9     print(l)
      
#### Merge Sort

归并并排序是分而治之的排序算法。
划分步骤很简单：将当前数组分成两半（如果N是偶数，则将其完全平等，或者如果N是奇数，则一边稍大于一个元素），然后递归地对这两半进行排序。
归并子例程合并两个（排序的）半部分以形成一个有序数组。归并排序非常适合分类非常大量的输入，因为O（N log N）比前面讨论的O（N2）排序算法增长得慢得多。

      1 def mergeSort(arr):
      2     if len(arr) >1:
      3         mid = len(arr)//2 #Finding the mid of the array
      4         L = arr[:mid] # Dividing the array elements
      5         R = arr[mid:] # into 2 halves
      6
      7         mergeSort(L) # Sorting the first half
      8         mergeSort(R) # Sorting the second half
      9
     10         i = j = k = 0
     11
     12         # Copy data to temp arrays L[] and R[]
     13         while i < len(L) and j < len(R):
     14             if L[i] < R[j]:
     15                 arr[k] = L[i]
     16                 i+=1
     17             else:
     18                 arr[k] = R[j]
     19                 j+=1
     20             k+=1
     21
     22         # Checking if any element was left
     23         while i < len(L):
     24             arr[k] = L[i]
     25             i+=1
     26             k+=1
     27
     28         while j < len(R):
     29             arr[k] = R[j]
     30             j+=1
     31             k+=1
 
 
#### Quicksort 
快速排序是另一个分而治之排序算法。选主元很重要，找头中尾3个数的中位数，例如8,12,3中位数为8，复杂点5个7个数选取中位数.Quicksort 演算法的陷阱相当多，须考虑数字全都相等、判断式是小于还是小于等于、分割点恰好选到最大值或者最小值、递回的区段范围、递回的区段很短、 ……等等问题。

      1 def sort(array=[12,4,5,6,7,3,1,15]):
      2     less = []
      3     equal = []
      4     greater = []
      5
      6     if len(array) > 1:
      7         pivot = array[0] #can be better
      8         for x in array:
      9             if x < pivot:
     10                 less.append(x)
     11             elif x == pivot:
     12                 equal.append(x)
     13             else x > pivot:
     14                 greater.append(x)
     15         return sort(less)+equal+sort(greater)
     16     else:
     17         return array
     
 使用列表生成式简写
 
      19  def qsort(arr):
     20     if len(arr) > 1:
     21         pivot = arr[0]
     22         return qsort([ for x in arr if x < pivot ]) + [pivot] + qsort([ for x in arr if x >= pivot ])
     23     else:
     24         return arr