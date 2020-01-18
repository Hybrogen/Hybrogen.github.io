---
layout: post
title: 剪花布条 HDU - 2087 题解
date: 2020-01-10
author: H_On
description: 使用 STL 中的 string 类型操作
tag: 题解
---

题目链接：[剪花布条 HDU - 2087](http://acm.hdu.edu.cn/showproblem.php?pid=2087)

### 题目
一块花布条，里面有些图案，另有一块直接可用的小饰条，里面也有一些图案。对于给定的花布条和小饰条，计算一下能从花布条中尽可能剪出几块小饰条来呢？

### 输入
输入中含有一些数据，分别是成对出现的花布条和小饰条，其布条都是用可见ASCII字符表示的，可见的ASCII字符有多少个，布条的花纹也有多少种花样。花纹条和小饰条不会超过1000个字符长。如果遇见#字符，则不再进行工作。

### 输出
输出能从花纹布中剪出的最多小饰条个数，如果一块都没有，那就老老实实输出0，每个结果之间应换行。

### 样例输入
```
abcde a3
aaaaaa  aa
#
```

### 样例输出
```
0
3
```

### 解题思路
利用 string 对象的方法进行模拟字符串匹配
* a.find(b) 方法可以从字符串 a 中获取字符串 b 第一次出现的位置
* a.size() 方法可以获取字符串 a 的长度
* a.erase(p0, p1) 使用两个迭代器地址 p0，p1 来删除字符串 a 中的一段字符
* a.begin() 获得字符串 a 的首字符迭代器地址

更详细的有关 string 和其他 STL 容器的信息可以去看猹的 STL 简介文章：[StandardTemplateLibrary](https://hybrogen.github.io/2020/01/STL1/)

首先找到 a 字符串中 b 第一次出现的位置，然后从 a 中删掉【第一个 b 】以及这个 b 之前的所有字符，防止前面的和后面的连起来又组成 b 串，每删掉一个计数加一。<br>
如此循环删除直到 a 串中找不到 b 串，输出计数。

### 代码
```c++
#include <iostream>
#include <string>

using namespace std;

int main() {
        ios::sync_with_stdio(0);
        cin.tie(0);
        string a;
        while (cin >> a) {
                // 如果遇到 # 停止程序
                if (a[0] == '#') break;
                string b; cin >> b;
                int c = 0;

                // 只要能从 a 中找到 b 就一直处理 a 串并且增加计数
                while (a.find(b) != -1) {
                        a.erase(a.begin(), a.begin() + (a.find(b) + b.size()));
                        c += 1;
                }

                // a.find(b) = -1 时说明 a 中找不到 b
                // 输出计数 c
                cout << c << endl;
        }
        return 0;
}
```
