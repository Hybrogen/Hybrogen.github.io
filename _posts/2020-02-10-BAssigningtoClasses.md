---
layout: post
title: 【Codeforces Round #618 (Div. 2)】B. Assigning to Classes 题解
date: 2020-02-10
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[B. Assigning to Classes](https://codeforces.com/contest/1300/problem/B)

### 题目
**Reminder: the median** of the array **[a1,a2,…,a2k+1]** of odd number of elements is defined as follows: let **[b1,b2,…,b2k+1]** be the elements of the array in the sorted order. Then median of this array is equal to **b<sub>k+1</sub>**.

There are **2n** students, the i-th student has skill level **a<sub>i</sub>**. It's **not guaranteed** that all skill levels are distinct.

Let's define **skill level of a class** as the median of skill levels of students of the class.

As a principal of the school, you would like to assign each student to one of the **2** classes such that each class has **odd number of students** (not divisible by **2**). The number of students in the classes may be equal or different, by your choice. Every student has to be assigned to exactly one class. Among such partitions, you want to choose one in which the absolute difference between skill levels of the classes is minimized.

What is the minimum possible absolute difference you can achieve?

### 输入
Each test contains multiple test cases. The first line contains the number of test cases **t (1≤t≤10<sup>4</sup>)**. The description of the test cases follows.

The first line of each test case contains a single integer **n (1≤n≤10<sup>5</sup>)** — the number of students halved.

The second line of each test case contains **2n** integers **a1,a2,…,a2n (1≤ai≤10<sup>9</sup>)** — skill levels of students.

It is guaranteed that the sum of **n** over all test cases does not exceed **10<sup>5</sup>**.

### 输出
For each test case, output a single integer, the minimum possible absolute difference between skill levels of two classes of odd sizes.

### 样例输入
```
3
1
1 1
3
6 5 4 1 2 3
5
13 4 20 13 2 5 8 3 17 16
```

### 样例输出
```
0
1
5
```

### 题意
中位数就是一堆数的最中间的那个数，如果是偶数那就取中间两个数的平均数。<br>
这道题让你把 2n 个数分成两部分，两部分都是奇数个数字，求两个数组的中位数的差值最小是多少。

### 解题思路
两个数组的**较大数**和**较小数**的数量越接近，两个中位数的差值越小。所以整个数组，对这 2n 个数排序之后，中间两个数的差值就是结果。

### 代码
```c++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
        ios::sync_with_stdio(0);
        cin.tie(0);
        int t; cin >> t;
        vector<int> v;
        while (t--) {
                v.clear();
                int n; cin >> n;
                n *= 2;
                int l = n;
                while (n--) {int a; cin >> a; v.push_back(a);}
                sort(v.begin(), v.end());
                cout << v[l/2] - v[l/2 - 1] << endl;
        }
        return 0;
}
```
