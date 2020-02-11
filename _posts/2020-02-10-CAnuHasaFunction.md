---
layout: post
title: 【Codeforces Round#618 (Div. 2)】C. Anu Has a Function 题解
date: 2020-02-10
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[C. Anu Has a Function](https://codeforces.com/contest/1300/problem/C)

### 题目
Anu has created her own function **f: f(x,y)=(x|y)−y** where **|** denotes [the bitwise OR operation](https://en.wikipedia.org/wiki/Bitwise_operation#OR). For example, **f(11,6)=(11|6)−6=15−6=9**. It can be proved that for any nonnegative numbers **x** and **y** value of **f(x,y)** is also nonnegative.

She would like to research more about this function and has created multiple problems for herself. But she isn't able to solve all of them and needs your help. Here is one of these problems.

A value of an array **\[a<sub>1</sub>,a<sub>2</sub>,…,a<sub>n</sub>\]** is defined as **f(f(…f(f(a<sub>1</sub>,a<sub>2</sub>),a<sub>3</sub>),…a<sub>n−1</sub>),a<sub>n</sub>)** (see notes). You are given an array with **not necessarily distinct** elements. How should you reorder its elements so that the value of the array is maximal possible?

### 输入
The first line contains a single integer **n (1≤n≤10^5)**.

The second line contains **n** integers **a<sub>1</sub>,a<sub>2</sub>,…,a<sub>n</sub> (0≤ai≤10^9)**. Elements of the array are **not guaranteed** to be different.

### 输出
Output **n** integers, the reordering of the array with maximum value. If there are multiple answers, print any.

### 样例
input
```
4
4 0 11 6
```
output
```
11 6 4 0
```
input
```
1
13
```
output
```
13
```

### 提示
In the first testcase, value of the array **\[11,6,4,0\]** is **f(f(f(11,6),4),0)=f(f(9,4),0)=f(9,0)=9**.

**\[11,4,0,6\]** is also a valid answer.




### 题意
有函数 `f(x, y) = x|y - y`
把输入的 n 个数按某种顺序排列，层层进行 f 函数，求得出来的值最大的排序方式。

### 解题思路
两个数位或之后再减去第二个数，实际上就是在二进制的角度把第一个数中与第二个数重复的 1 给去掉。<br>
例：11 | 6 - 6

|十进制|二进制|
|:---:|:---:|
|11|1011|
|6|110|
|11 \| 6 = 15|1111|
|5 - 6 = 9|1001|

从 `15 - 6 = 9` 的二进制可以发现，减法实际上就是从 15 里把与 6 对应的 1 给去掉。从结果来看 9 实际上就是从 11 中，把 6 中对应位置也有的 1 给删掉。所以要最大的结果，就是一直减去对应位置的 1 之后，剩下的数最大即可。因此只需要确定排在最前面的数，后面的数顺序都无所谓。
> 首先这个数需要有 **单独存在** 的 **1**<br>
> 其次如果有多个数都有单独存在的 1 ，需要找到 **剩下的 1 位数最高** 的数

顺便，这里借用了 bitset 容器，bitset 容器也是 STL 里的一个容器，由于太过不常用，所以我依然不考虑做专门介绍。这里简单说明：
1. 容器声明的时候尖括号里写的是位容器的位数
2. 给位容器赋值可以用等于号赋值 `bitset<50> b = a[i];`
3. `b[i]` 可以访问第 i 位的值（0 或 1）【0 号位置是**最低位**

### 代码
```c++
#include <iostream>
#include <bitset>

using namespace std;

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        int w[32], s[32], a[100009];
        for (int i = 0; i < 32; i++) w[i] = s[i] = 0;
        int t; cin >> t;
        for (int i = 0; i < t; i++) {
                cin >> a[i]; bitset<32> b = a[i];
                for (int j = 0; j < 32; j++) if (b[j]) w[j] += 1, s[j] = a[i];
        }
        int he;
        for (int i = 31; i >= 0; i--) if (w[i] == 1) {he = s[i]; break;}
        for (int i = 0; i < t; i++) if (a[i] == he) {
                int t = a[0]; a[0] = a[i]; a[i] = t;
                break;
        }
        for (int i = 0; i < t; i++) {
                if (i) cout << ' ';
                cout << a[i];
        } cout << endl;
        return 0;
}
```
