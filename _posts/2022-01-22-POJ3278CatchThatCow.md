---
layout: post
title: 【回炉重造】POJ-3278.Catch That Cow 题解
date: 2022-01-22
author: H_On
description: 我好弱，重点记录一个没想到的剪枝
tag: 题解
---

题目链接：[POJ-3278.Catch That Cow](https://vjudge.net/problem/POJ-3278/origin)

### 题目
![题面](/images/20220122/Q.png)

### 题意
有一个一维的坐标轴，你在一个位置 `X`，牛在另一个位置 `T`。<br>
你可以这样移动：
1. 左移一步 X-1
2. 右移一步 X+1
3. 传送一下 X*2

问你最少要几步能找到牛

### 解题思路
广搜：
1. 试试+1能不能走，能走就加入队列，并记录走到这一步所需要的时间
2. 试试-1能不能走，能走就加入队列，并记录走到这一步所需要的时间
3. 试试<<1(*2)能不能走，能走就加入队列，并记录走到这一步所需要的时间
4. 如果遇到牛就返回时间

注意：
* 每一种情况都有判断条件，位置队列和时间队列一定要同步更新
* 注意范围，小于 0 和大于 100000 的都不能走，应该舍弃
* **设置一个标记数组**，记录已经走过的位置，避免重复（剪枝）（我就是没想到加这个标记数组导致一直超时）
* 注意如果初始位置就是牛的位置，要输出0

### 代码
```cpp
//
// _   _     ___           ____ ___  ____  _____
//| | | |   / _ \ _ __    / ___/ _ \|  _ \| ____|
//| |_| |  | | | | '_ \  | |  | | | | | | |  _|
//|  _  |  | |_| | | | | | |__| |_| | |_| | |___
//|_| |_|___\___/|_| |_|  \____\___/|____/|_____|
//     |_____|
//

#include <iostream>
using namespace std;
typedef int hip;
#include <queue>

hip s, e;
bool f[100009];// 这个标记数组的剪枝是最关键的！

hip bfs() {
        if (s==e) return 0;
        queue<hip> R, T;
        R.push(s);
        T.push(0);
        for (hip i=0;i<100009;i++) f[i]=true;
        f[s] = false;
        while (!R.empty()) {
                hip tr=R.front(); R.pop();
                hip tt=T.front(); T.pop();
                if (tr+1==e || tr-1==e || tr<<1==e) return tt+1;
                if (tr<=50000 && tr<e && f[tr<<1]) R.push(tr<<1), T.push(tt+1), f[tr<<1] = false;
                if (tr<100000 && tr<e && f[tr+1]) R.push(tr+1), T.push(tt+1), f[tr+1] = false;
                if (tr>0 && f[tr-1]) R.push(tr-1), T.push(tt+1), f[tr-1] = false;
        }
        return 0;
}

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        while (true) {
                cin >> s >> e;
                cout << bfs() << endl;
        }
        return 0;
}
```
