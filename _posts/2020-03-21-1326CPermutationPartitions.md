---
layout: post
title: 【Codeforces Global Round 7】C. Permutation Partitions 题解
date: 2020-03-21
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[C. Permutation Partitions](http://codeforces.com/contest/1326/problem/C)

### 题目
![C 题题面 1](/images/20200321/C1.png)
![C 题题面 2](/images/20200321/C2.png)

### 题意
给你 n 个数，把这些数分成 k 组，每组里的最大值相加必须最大，也就是说：<br>
找到这 n 个树中最大的 k 个数，这些数必须各自存在于单独的一组

### 解题思路
这道题其实就是高中学 Cnm 的那种排列组合的分组问题，简单来说就是插卡<br>
插一张卡，分成两份，插两张卡，分成三份。。。<br>
而这道题更简单，分组的间隔已经明确告诉你了，你只需要求出每两个标值中间有几种插卡方式即可
1. 用 map 将【数-下标】 **从大到小** 存起来
2. 求出最大的 k 个数，并将这些数的下标 **从小到大** 存起来
3. 两个数中间间隔的人数（+1）即为分组的方法数
4. 注意第一个人前面的所有人肯定都是跟第一个人一组的，还有记得要取余

### 代码
```cpp
#include <iostream>
using namespace std;
typedef long long hip;
#include <set>
#include <map>
#include <vector>
const hip maxn = 998244353;

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        hip n, k; cin >> n >> k;
        vector<hip> v;
        map<hip, hip, greater<hip>> m;
        for (hip i = 0; i < n; i++) {
                hip a; cin >> a;
                v.push_back(a);
                m[a] = i;
        }
        hip r1 = 0;
        set<hip> s;
        for (auto a : m) {
                k -= 1;
                r1 += a.first;
                s.insert(a.second);
                if (k == 0) break;
        }
        hip r = 1;
        k = *s.begin();
        for (auto a : s) {
                n = a - k; k = a;
                if (n > 0) r = r*n % maxn;
        }
        cout << r1 << ' ' << r << endl;
        return 0;
}
```
