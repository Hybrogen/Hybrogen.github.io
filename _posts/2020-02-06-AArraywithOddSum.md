---
layout: post
title: 【寒假CodeForces】A. Array with Odd Sum 题解
date: 2020-02-06
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[A. Array with Odd Sum](https://codeforces.com/contest/1296/problem/A)

### 题目
You are given an array a consisting of n integers.

In one move, you can choose two indices **1≤i,j≤n** such that **i≠j** and set **ai:=aj**. You can perform such moves any number of times (possibly, zero). You can choose different indices in different operations. The operation := is the operation of assignment (i.e. you choose **i** and **j** and replace **ai** with **aj**).

Your task is to say if it is possible to obtain an array with an odd (not divisible by **2**) sum of elements.

You have to answer t independent test cases.

### 输入
The first line of the input contains one integer **t (1≤t≤2000)** — the number of test cases.

The next **2t** lines describe test cases. The first line of the test case contains one integer **n (1≤n≤2000)** — the number of elements in a. The second line of the test case contains n integers **a1,a2,…,an (1≤ai≤2000)**, where **ai** is the **i-th** element of **a**.

It is guaranteed that the sum of n over all test cases does not exceed **2000 (∑n≤2000)**.

### 输出
For each test case, print the answer on it — "YES" (without quotes) if it is possible to obtain the array with an odd sum of elements, and "NO" otherwise.

### 样例输入
```
5
2
2 3
4
2 2 8 8
3
3 3 3
4
5 5 5 5
4
1 1 1 1
```

### 样例输出
```
YES
NO
YES
NO
NO
```

### 题意
你可以把数列里的一个数的值变成数列里另一个数的值，随便变，变几次都行。然后如果你能使这 n 个数的和为**奇数**，那你就成功了。如果你不能把数列变得求和为奇数，那就输出 `NO` 。

### 解题思路
其实计算一下**原数列**中奇数和偶数的个数即可。<br>
要我们得出和为奇数，其实就是需要有 **“奇数个奇数”** ，所以我们只需要判断三种情况：
1. **有奇数个奇数** - 那就说明总和肯定是奇数；
2. **有奇数有偶数** - 轮到这种情况说明奇数肯定是偶数个，这种情况下我们只需要把一个偶数变成奇数（或者把一个奇数变成偶数）即可，所以也是满足要求的；【顺便其实只要**有奇有偶**，那就肯定可以利用偶数来获得奇数个奇数
3. 如果上面两种情况都不满足，那就说明这个数列 “全为奇数而且总共偶数个” 或者 “全为偶数” ，显然是怎么变都徒劳无功的，所以输出 NO 。

### 代码
```c++
#include <iostream>

using namespace std;

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        int t; cin >> t;
        while (t--) {
                int n; cin >> n;
                int o = 0, e = 0;
                while (n--) {
                        int a; cin >> a;
                        if (a % 2) o += 1;
                        else e += 1;
                }
                if (o % 2) cout << "YES" << endl;
                else if (o && e) cout << "YES" << endl;
                else cout << "NO" << endl;
        }
        return 0;
}
```
