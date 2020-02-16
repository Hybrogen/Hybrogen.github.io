---
layout: post
title: 【Codeforces Round#620 (Div. 2)】C. Air Conditioner 题解
date: 2020-02-15
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[C. Air Conditioner](https://codeforces.com/contest/1304/problem/C)

### 题目
Gildong owns a bulgogi restaurant. The restaurant has a lot of customers, so many of them like to make a reservation before visiting it.

Gildong tries so hard to satisfy the customers that he even memorized all customers' preferred temperature ranges! Looking through the reservation list, he wants to satisfy all customers by controlling the temperature of the restaurant.

The restaurant has an air conditioner that has 3 states: _off, heating,_ and _cooling_. When it's _off_, the restaurant's temperature remains the same. When it's heating, the temperature increases by 1 in one minute. Lastly, when it's cooling, the temperature decreases by 1 in one minute. Gildong can change the state as many times as he wants, at any integer minutes. The air conditioner is _off_ initially.

Each customer is characterized by three values: **ti** — the time (in minutes) when the **i-th** customer visits the restaurant, **li** — the lower bound of their preferred temperature range, and **hi** — the upper bound of their preferred temperature range.

A customer is satisfied if the temperature is within the preferred range at the instant they visit the restaurant. Formally, the **i-th** customer is satisfied if and only if the temperature is between **li** and **hi** (inclusive) in the **ti-th** minute.

Given the initial temperature, the list of reserved customers' visit times and their preferred temperature ranges, you're going to help him find if it's possible to satisfy all customers.

### 输入
Each test contains one or more test cases. The first line contains the number of test cases **q (1≤q≤500)**. Description of the test cases follows.

The first line of each test case contains two integers **n** and **m (1≤n≤100, −10^9≤m≤10^9)**, where n is the number of reserved customers and m is the initial temperature of the restaurant.

Next, n lines follow. The **i-th** line of them contains three integers **ti, li**, and **hi (1≤ti≤10^9, −109≤li≤hi≤10^9)**, where **ti** is the time when the **i-th** customer visits, **li** is the lower bound of their preferred temperature range, and **hi** is the upper bound of their preferred temperature range. The preferred temperature ranges are **inclusive**.

The customers are given in non-decreasing order of their visit time, and the current time is **0**.

### 输出
For each test case, print "YES" if it is possible to satisfy all customers. Otherwise, print "NO".

You can print each letter in any case (upper or lower).

### 样例
input
```
4
3 0
5 1 2
7 3 5
10 -1 0
2 12
5 7 10
10 16 20
3 -100
100 0 0
100 -50 50
200 100 100
1 100
99 -100 0
```
output
```
YES
NO
YES
NO
```

### 题意
输入一个 t 组样例<br>
输入一个人数 n ；店内初始温度 itime ；<br>
输入 n 行：每行是 这个人到店时间 atime ；这个人能承受的温度范围 a b ；<br>
每一分种你可以调高一度或者调低一度或者不调。

问你能否让所有老板都满意。

### 解题思路
* 因为给的顾客的到店时间都是递增的，所以直接逐个判断
* 先求出从上一个顾客到这个顾客之间的时间可以让温度变化的范围 - pa 和 pb
* 如果**可达到的温度范围**与**现在这个人的温度范围**没有交集，**直接判定为**错误**
* 如果有交集，求出最小区间作为下一次的**上一个范围**继续循环

### 代码
```c++
#include <iostream>
#include <vector>

using namespace std;

struct P {int t, a, b;};

void EX(int &a, int &b) {int t = a; a = b; b = t;}

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        int t; cin >> t;
        while (t--) {
                int n, c; cin >> n >> c;
                int pn = c;
                P pp = {0, c, c};
                bool f = true;
                while (n--) {
                        P p; cin >> p.t >> p.a >> p.b;
                        if (f) {
                                int pa = pp.a - (p.t - pp.t), pb = pp.b + (p.t - pp.t);
                                if (pa > pb) EX(pa, pb);
                                if (p.a > pb || p.b < pa) f = false;
                                pp.t = p.t;
                                pp.a = max(p.a, pa);
                                pp.b = min(p.b, pb);
                        }
                }
                if (f) cout << "YES" << endl;
                else cout << "NO" << endl;
        }
        return 0;
}
```
