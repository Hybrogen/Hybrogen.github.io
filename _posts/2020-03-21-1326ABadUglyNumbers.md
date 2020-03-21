---
layout: post
title: 【Codeforces Global Round 7】A. Bad Ugly Numbers 题解
date: 2020-03-21
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[A. Bad Ugly Numbers](http://codeforces.com/contest/1326/problem/A)

### 题目
![A 题题面 1](/images/20200321/A1.png)
![A 题题面 2](/images/20200321/A2.png)

### 题意
给你一个数字 n ，输出一个数字 s ，s 满足一些要求
1. s 的位数等于 n ，即 s 由 n 个数字组成
2. s 这个数不能被其中的任何一位整除

### 解题思路
* 如果是一位数的话，它当然可以被自己整除，所以直接输出 `-1`
* 如果是多位数找一个特殊的数即可，如 `233` 或者 `277`

### 代码
```cpp
#include <iostream>
using namespace std;
typedef int hip;

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        hip i = 0, t;
        for (cin >> t; i < t; i++) {
                hip a; cin >> a;
                if (a == 1) cout << -1 << endl;
                else {
                        cout << 2;
                        while (--a) cout << 3;
                        cout << endl;
                }
        }
        return 0;
}
```
