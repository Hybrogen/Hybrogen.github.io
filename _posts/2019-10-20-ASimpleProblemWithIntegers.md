---
layout: post
title: A Simple Problem with Integers - 线段树模板题
date: 2019-10-20
description: 有关线段树的使用的例题
tag: 题解
---

[题目链接 - POJ3468](http://poj.org/problem?id=3468)

### 题目
You have N integers, A1, A2, ... , AN. You need to deal with two kinds of operations. One type of operation is to add some given number to each number in a given interval. The other is to ask for the sum of numbers in a given interval.

**H_On の 贴心翻译：**你有 N 个整数，A1，A2，. . . ，AN。现在你需要处理两种操作：一种是把一段指定区间的数字都加上一个指定的数；另一种是去访问一段区间的数字的和。

### 输入
The first line contains two numbers N and Q. 1 ≤ N,Q ≤ 100000.
The second line contains N numbers, the initial values of A1, A2, ... , AN. -1000000000 ≤ Ai ≤ 1000000000.
Each of the next Q lines represents an operation.
"C a b c" means adding c to each of Aa, Aa+1, ... , Ab. -10000 ≤ c ≤ 10000.
"Q a b" means querying the sum of Aa, Aa+1, ... , Ab.

**H_On の 贴心翻译：**第一行包含两个数 N 和 Q ，1 ≤ N, Q ≤ 100000 ；
第二行是 N 个整数，它们的值分别是 A1，A2，. . . ，AN，-1000000000 ≤ Ai ≤ 1000000000 ；
接下来的 Q 行各代表一个操作；
"C a b c" 意味着把 c 加道每一个 Aa，Aa+1，. . . ，Ab，-10000 ≤ c ≤ 10000 ；
"Q a b" 意味着访问 Aa，Aa+1，... ，Ab 这些数的和。

### 输出
You need to answer all Q commands in order. One answer in a line.

**H_On の 贴心翻译：**你需要对于每一个 Q 命令输出一个单独占一行的整数。

### 样例输入
```
10 5
1 2 3 4 5 6 7 8 9 10
Q 4 4
Q 1 10
Q 2 4
C 3 6 3
Q 2 4
```

### 样例输出
```
4
55
9
15
```

### 提示
The sums may exceed the range of 32-bit integers.

**H_On の 贴心翻译：**这些数的和可能超过 32 位整数。

### 解题思路
线段树的基础操作：
1. 建树（求和数）
2. 区间更新（懒标记）
3. 区间查询（懒标记）
4. 就是非常普通的线段树，请熟悉二叉树的建树原理和子节点的访问原理 [参考资料](https://hybrogen.github.io/2019/09/SegmentTree/) [参考资料](https://hybrogen.github.io/2019/09/SegmentTree2/)

### 代码
数组写法
```
将会在一周后放出
```

结构体写法
```
将会在一周后放出
```

### 说一下
看提示啊，人家都说了超过 32 位，那肯定用 long long 就可以做，不然大概会说超过 64 位吧 =w=<br>
总之在懒标记和储存数的部分都用 long long 就好了

然后还有就是，**复制需谨慎**，我复制了几行结构差不多的代码，由于我的变量大多都一个字母，有一个没改过来，样例能过，一直交一直 WA ，最后发现居然是有一个地方的变量写错了，就很绝望
