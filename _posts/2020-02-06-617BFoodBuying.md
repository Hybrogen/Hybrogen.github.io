---
layout: post
title: 【Codeforces Round#617 (Div. 3)】B. Food Buying 题解
date: 2020-02-06
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[B. Food Buying](https://codeforces.com/contest/1296/problem/B)

### 题目
Mishka wants to buy some food in the nearby shop. Initially, he has s burles on his card.

Mishka can perform the following operation any number of times (possibly, zero): choose some **positive integer number 1≤x≤s**, buy food that costs exactly **x** burles and obtain **⌊x/10⌋** burles as a cashback (in other words, Mishka spends **x** burles and obtains **⌊x/10⌋** back). The operation **⌊a/b⌋** means **a** divided by **b** rounded down.

It is guaranteed that you can always buy some food that costs x for any possible value of **x**.

Your task is to say the maximum number of burles Mishka can spend if he buys food optimally.

For example, if Mishka has **s=19** burles then the maximum number of burles he can spend is **21**. Firstly, he can spend **x=10** burles, obtain **1** burle as a cashback. Now he has **s=10** burles, so can spend **x=10** burles, obtain **1** burle as a cashback and spend it too.

You have to answer **t** independent test cases.

### 输入
The first line of the input contains one integer **t (1≤t≤10^4)** — the number of test cases.

The next t lines describe test cases. Each test case is given on a separate line and consists of one integer **s (1≤s≤10^9)** — the number of burles Mishka initially has.

### 输出
For each test case print the answer on it — the maximum number of burles Mishka can spend if he buys food optimally.

### 样例输入
```
6
1
10
19
9876
12345
1000000000
```

### 样例输出
```
1
11
21
10973
13716
1111111111
```

### 题意
你每花 n 块钱，都能获得 ⌊x/10⌋ 块钱（⌊x/10⌋ 的意思是 x/10 向下取整），问你最多能花多少钱。

### 解题思路
既然向下取整的话那不就是说个位数的钱花出去就是白给吗，所以能花整十块就只花整十块。<br>
所以我们把 `n % 10` 留下来，把 `n - (n % 10)` 花出去然后获得 `n/10` 块钱，剩下的钱数就是 `n = n/10 + (n % 10)` 。一直把钱花完就好。硬要说也是简单模拟吧。

### 代码
```c++
#include <iostream>

typedef long long ll;

using namespace std;

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        int t; cin >> t;
        while (t--) {
                ll n; cin >> n;
                ll s = 0;
                while (n) {
                        if (n > 9) s += n - (n % 10), n = n/10 + (n % 10);
                        else s += n, n = 0;
                }
                cout << s << endl;
        }
        return 0;
}
```
