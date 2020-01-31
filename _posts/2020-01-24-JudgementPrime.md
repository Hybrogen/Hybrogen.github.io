---
layout: post
title: 【一败涂地】L1-028 判断素数 (10分) 的坑
date: 2020-01-24
author: H_On
description: 关于素数判断的卡时间问题
tag: 题解
---

题目链接：[L1-028 判断素数 (10分)](https://pintia.cn/problem-sets/994805046380707840/problems/994805106325700608)

### 题目
本题的目标很简单，就是判断一个给定的正整数是否素数。

### 输入
输入在第一行给出一个正整数`N` (≤ 10），随后`N`行，每行给出一个小于2^​31的需要判断的正整数。

### 输出
对每个需要判断的正整数，如果它是素数，则在一行中输出`Yes`，否则输出`No`。

### 样例输入
```
2
11
111
```

### 样例输出
```
Yes
No
```

### 解题思路
`2^31` 对应的十进制数是`2147483648`，是一个十位数，按照正常的思路就是写一个函数，遍历 **2到根号n** 中间的所有数，如果其中有一个数可以把n整除，那么说明n不是素数。
```c++
bool is_prime(int n) {
        for (int i = 2; i < sqrt(n); i++) if (n % i == 0) return false;
        return true;
}
```
而取根号比较比较麻烦，又要声明一个`<cmath>`函数库，sqrt()函数的值又不整，有时会让人有点难受。（当然我就随便说说，如果你不以为然请不要喷我> _ <）<br>
所以出现了下面这种写法
```c++
bool is_prime(int n) {
        for (int i = 2; i*i < n; i++) if (n % i == 0) return false;
        return true;
}
```
用`i 的平方`来表示对`根号 n`的控制。。。**但是！**<br>
这道题用`i*i < n`的方法来判断，第二个样例就时间超限，但是`i < sqrt(n)`的方法来判断就能 AC 。<br>
我在写博客写到这里突然想起来要去探索一下原因，原来原因很简单，因为有符号整数的取值范围就是`-2^31 ~ 2^31`，这道题的取值范围也是到 2^31 所以 i*i 有可能会超过 int 的范围。而一个很神秘的是，有的时候如果数据大于 int （即需要使用 long long）但是变量类型是 int ，oj 它不显示`答案错误`而是显示`时间超限`。所以如果用`i*i < n`的话 i 要是 long long<br>
代码改成这样就能过了=w=
```c++
bool is_prime(long long n) {
        for (long long i = 2; i < sqrt(n); i++) if (n % i == 0) return false;
        return true;
}
```

### 代码
```c++
#include <iostream>

typedef long long ll;

using namespace std;

bool is_prime(ll a) {
        if (a == 1) return false;
        for (ll i = 2; i*i < a; i++) if (a % i == 0) return false;
        return true;
}

int main() {
        ios::sync_with_stdio(0);
        cin.tie(0);
        int t; cin >> t;
        while (t--) {
                ll a; cin >> a;
                if (is_prime(a)) cout << "Yes" << endl;
                else cout << "No" << endl;
        }
        return 0;
}
```
或者
```c++
#include <iostream>
#include <cmath>

using namespace std;

bool is_prime(int a) {
        if (a == 1) return false;
        int n = sqrt(a);
        for (int i = 2; i < n; i++) if (a % i == 0) return false;
        return true;
}

int main() {
        ios::sync_with_stdio(0);
        cin.tie(0);
        int t; cin >> t;
        while (t--) {
                int a; cin >> a;
                if (is_prime(a)) cout << "Yes" << endl;
                else cout << "No" << endl;
        }
        return 0;
}
```
