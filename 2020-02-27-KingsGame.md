---
layout: post
title: HDU 5643 King's Game 题解 - 约瑟夫环问题简结
date: 2020-02-27
description: 遇到过没记住的约瑟夫环解决方法
tag: 题解
---

[题目链接 - HDU5643](http://acm.hdu.edu.cn/showproblem.php?pid=5643)

### 题目
In order to remember history, King plans to play losephus problem in the parade gap.He calls **n(1≤n≤5000)** soldiers, counterclockwise in a circle, in label 1,2,3...n.<br>
The first round, the first person with label 1 counts off, and the man who report number 1 is out.<br>
The second round, the next person of the person who is out in the last round counts off, and the man who report number 2 is out.<br>
The third round, the next person of the person who is out in the last round counts off, and the person who report number 3 is out.<br>
The N - 1 round, the next person of the person who is out in the last round counts off, and the person who report number n−1 is out.<br>
And the last man is survivor. Do you know the label of the survivor?

**H_On の 贴心翻译：**<br>
为了铭记历史，国王准备van♂个游戏。他叫来 **n(1≤n≤5000)** 个士兵，叫他们围成一个圈，分别标上 1-n 。
* 第 1 轮，标号为 1 的人开始计数，报到 1 的人出队，开始第 2 轮
* 第 2 轮，从上一轮出队的人的下一个人开始报数，报到 2 的人出队，开始第 3 轮
* 第 3 轮，从上一轮出队的人的下一个人开始报数，报到 3 的人出队，开始第 4 轮
* 第 n - 1 轮，从上一轮出队的人的下一个人开始报数，报到 n - 1 的人出队

最后剩下的人将会幸存。你知道那个人的标号是什么吗？

### 输入
The first line contains a number **T(0\<T≤5000)**, the number of the testcases.<br>
For each test case, there are only one line, containing one integer n, representing the number of players.

**H_On の 贴心翻译：**第一行是一个测试样例数 T(0\<T≤5000)。每一组样例有一个整数 n 表示士兵人数。

### 输出
Output exactly T lines. For each test case, print the label of the survivor.

**H_On の 贴心翻译：**输出 T 行。对于每一组测试样例，输出一个幸存者的标签。

### 样例输入
```
2
2
3
```

### 样例输出
```
2
2
```

### 解题思路
这种转圈选人问题，被称作 “约瑟夫环问题” ，约瑟夫环实际上是一种动态规划问题，因为暴力模拟的话很容易解决不了大数据问题，因此我们需要推出不同人数选人结果之间的关系。

约瑟夫环是一个递推和逆向思维的原理，直接看代码比较难以理解，可以参考猹的另一篇博客来理解这道题[H_On简结 约瑟夫环问题](http://blog.zinchon.cn/2020/02/JosephusProblem/)

### 代码
```c++
#include <iostream>
using namespace std;

int F(int n) {
        int r = 0, m = n - 1;
        for (int i = 2; i <= n; i += 1, m -= 1) r = (r + m) % i;
        return r + 1;
}

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        int t; cin >> t;
        while (t--) {
                int a; cin >> a;
                cout << F(a) << endl;
        }
        return 0;
}
```
