---
layout: post
title: 【Codeforces Round#621 (Div. 1 + Div. 2)】C. Cow and Message 题解
date: 2020-02-19
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[C. Cow and Message](https://codeforces.com/contest/1307/problem/C)

### 题目
一直觉得英文题题面很难理解，然后也有题目链接，很纠结要不要每次都把题目再写一遍，昨天看大佬题解是贴一个截图上去，感觉也不错，以后我就这么干辣

![C 题题面 1](/images/20200219/C1.png)
![C 题题面 2](/images/20200219/C2.png)

### 题意
有一个字符串，里面有很多子序列，组成子序列的字母在字符串中的位置组成一个 **等差数列** ，这些子序列可能有好多种组成方式，问你在所有的子序列的组成方式的个数中的最大值是多少。

### 解题思路
害，一开始没意识到位置只能是等差数列，只在想排列组合了。<br>
在题意的情况下，只有一个字母组成的子序列和两个字母组成的子序列中有可能出现最大值。所以我们就想办法找出两个字母组合的最大值就可以了。（一个字母的可以直接在输入的时候求最大值）<br>
先遍历一遍字符串
1. 把字符的个数存起来
2. 单个字符的最大值求出来
3. 把每个字符串出现的位数给存起来

再遍历一遍字符串
1. 将当前字母 `t[i]` 作为第一个字母
2. 遍历 26 个字母找到存在于字符串中的字符，作为第二个字母 c
3. 确定出字符串中 c 在 t[i] 之后的字串里出现的次数，这里使用了 `upper_bound()` 函数找到 c 的所有位置中第一个大于 i 的数的位置，因为返回的是迭代器地址，使用 `v[c].end() - p` 得出后面的 c 的个数
4. 将两个字母作为一个字符串，通过 map 储存这个字符串出现的次数，同时取最大值

数据很大，记得用 `long long`

### 代码
```cpp
#include <iostream>
using namespace std;
#include <map>
#include <vector>
#include <algorithm>

typedef long long ll;

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        string t; cin >> t;
        map<char, vector<ll>> v;
        map<char, ll> m;
        map<string, ll> o;
        ll l = t.size(), r = 0;
        for (ll i = 0; i < l; i++) m[t[i]] += 1, r = max(m[t[i]], r), v[t[i]].push_back(i);
        for (ll i = 0; i < l; i++) {
                for (char c = 'a'; c <= 'z'; c++) {
                        if (!m[c]) continue;
                        vector<ll>::iterator p = upper_bound(v[c].begin(), v[c].end(), i);
                        if (p == v[c].end()) continue;
                        string s; s += t[i]; s += c;
                        o[s] += v[c].end() - p;
                        r = max(r, o[s]);
                }
        }
        cout << r << endl;
        return 0;
}
```
