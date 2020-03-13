---
layout: post
title: 【Codeforces Round#593 (Div. 2)】B. Alice and the List of Presents 题解 - 快速幂
date: 2020-02-14
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[B. Alice and the List of Presents](https://codeforces.com/problemset/problem/1236/B)

### 题目
Alice got many presents these days. So she decided to pack them into boxes and send them to her friends.

There are **n** kinds of presents. Presents of one kind are identical (i.e. there is no way to distinguish two gifts of the same kind). Presents of different kinds are different (i.e. that is, two gifts of different kinds are distinguishable). The number of presents of each kind, that Alice has is very big, so we can consider Alice has an infinite number of gifts of each kind.

Also, there are **m** boxes. All of them are for different people, so they are pairwise distinct (consider that the names of **m** friends are written on the boxes). For example, putting the first kind of present into the first box but not into the second box, is different from putting the first kind of present into the second box but not into the first box.

Alice wants to pack presents with the following rules:
* She won't pack more than one present of each kind into the same box, so each box should contain presents of different kinds (i.e. each box contains a subset of **n** kinds, empty boxes are allowed);
* For each kind at least one present should be packed into some box.

Now Alice wants to know how many different ways to pack the presents exists. Please, help her and calculate this number. Since the answer can be huge, output it by modulo **10^9+7**.

See examples and their notes for clarification.

### 输入
The first line contains two integers **n** and **m**, separated by spaces **(1≤n,m≤10^9)** — the number of kinds of presents and the number of boxes that Alice has.

### 输出
Print one integer  — the number of ways to pack the presents with Alice's rules, calculated by modulo **10^9+7**

### 样例
input
```
1 3
```
output
```
7
```
input
```
2 2
```
output
```
9
```

### 题意
你有 n 种礼物，m 个礼盒<br>
* 每种礼物都有无数个
* 每个礼盒里可以不装礼物，每个礼盒里不能装两个或多个相同的礼物
* 每种礼物最少选一次

问你有几种装礼物的方式。

### 解题思路
* 对于每一种礼物，都有 **装** 和 **不装** 两种状态，扩展到所有礼盒，总共就有 **2^m** 种情况，由于每种礼物至少被取一次，所以要减去全都不取的情况。<br>
> 所以对于一种礼物，取法就是 **2^m - 1**
* 对于所有礼物就是每一种礼物单独情况的叠加
> 所以总共的取法就是 **(2^m - 1)^n**

由于给定的幂数也特别大，所以需要使用**快速幂**来求幂数，快速幂其实就是把 `3^6` 变成 `(3^3)^2` 这种感觉，看看代码应该很容易理解了

### 代码
这篇文章实际上是我 20200216 编辑的哈，为了方便把工作和时间对应起来于是归在 14 号的文章了，于是乎<br>
对于这个代码里的 `template <typename H_On_infnT> class infn {public: H_On_infnT N; infn(H_On_infnT honValue) {this->N = honValue;}};` 有不解的话，可以移步今天刚完成的新知识总结[H_On Template - H_On 自制程序模板推荐](https://hybrogen.github.io/2020/02/H_OnTemplate/)
```cpp
#include <iostream>
using namespace std;

typedef long long ll;

template <typename H_On_infnT> class infn {public: H_On_infnT N; infn(H_On_infnT honValue) {this->N = honValue;}};

infn<ll> INF(ll(1e9 + 7));

ll qpow(ll d, ll m) {
        ll r = 1;
        while (m) {
                if (m & 1) r = r*d % INF.N;
                d = d*d % INF.N;
                m >>= 1;
        }
        return r;
}

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        ll n, m; cin >> n >> m;
        ll r = qpow(2, m) - 1;
        cout << qpow(r, n) % INF.N << endl;
        return 0;
}
```
