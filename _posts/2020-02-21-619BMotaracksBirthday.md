---
layout: post
title: 【Codeforces Round#619 (Div. 2)】B. Motarack's Birthday 题解
date: 2020-02-21
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[B. Motarack's Birthday](https://codeforces.com/contest/1301/problem/B)

### 题目
![B 题题面 1](/images/20200221/B1.png)
![B 题题面 2](/images/20200221/B2.png)

### 题意
给你 n 个数，其中有一些可能被替换成了 `-1` 。现在让你用一个数 `k` 替换所有的 `-1` ，使两个相邻的数 `差的绝对值的最大值 m` 最小。<br>
输出 k 和 m 的值。

### 解题思路
这个题主要是需要处理 `-1` 与旁边 `不是 -1 的数` 的关系。
* 连续的 -1 替换以后相邻差值是 0 不用管
* 连续的非 -1 数邻差直接在遍历的时候记下最大值即可

然而 k 的值如何确定呢，因为替换以后的 k 与相邻的数的最大的差值肯定是出现在不需要替换的最大和最小的数旁边，因此我们要获取这两个数。用这两个数确定出平均数就是差值最小的，再将这个数与两端的数的差值与之前存的最大差值求最大，即得结果。【注释写的差不多了

### 代码
```cpp
#include <iostream>
using namespace std;
#include <set>
#include <map>
#include <vector>
#include <algorithm>

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        int t; cin >> t;
        while (t--) {
                int n; cin >> n;
                //输入原数据
                vector<int> v;
                for (int i = 0; i < n; i++) {int a; cin >> a; v.push_back(a);}
                //f - 用过的数的标记
                map<int, int> f;
                f[-1] = 1; f[n] = 1;
                //最大相邻的差
                int m = 0;
                //获取与 -1 相邻的数
                vector<int> s;
                for (int i = 0; i < n; i++) if (v[i] == -1) {
                        int j = i - 1;
                        if (f[j] == 0 && v[j] != -1) s.push_back(v[j]), f[j] = 1;
                        j = i + 1;
                        if (f[j] == 0 && v[j] != -1) s.push_back(v[j]), f[j] = 1;
                } else if (i && v[i - 1] != -1) m = max(m, abs(v[i] - v[i - 1]));
                //如果全是 -1 或者没有 -1
                if (!s.size()) {cout << m << " 0" << endl; continue;}
                //找出所有需要判断的数的中值，并再次获取一下最大相邻差值
                sort(s.begin(), s.end());
                int r = (s[0] + s[s.size() - 1]) >> 1;
                m = max(m, max(s[s.size() - 1] - r, r - s[0]));
                cout << m << ' ' << r << endl;
        }
        return 0;
}
```
