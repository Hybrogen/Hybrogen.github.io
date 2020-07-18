---
layout: post
title: 【牛客2020暑假多校联赛】C.Operation Love 题解
date: 2020-07-18
author: H_On
description: 真的有比去年好一点吗？
tag: 题解
---

题目链接：[C](https://ac.nowcoder.com/acm/contest/view-submission?submissionId=44284554)

### 题目
![C 题题面 1](/images/20200718/C1.png)
![C 题题面 2](/images/20200718/C2.png)

### 题意
一共 **t** 组多实例，每组输入 **20** 个坐标，表示一个手的各个坐标，题上给的示例是**右手**的样子，左手与右手**对称**，手的**大小不变，位置会变，本身也会旋转**，问你给定的手是`左手`还是`右手`

### 解题思路
我们一队人讨论了半天判定方法，最后发现一个关键: **各个边长都不会变，一只手无论怎么转，各边的相对位置不会变**<br>
最后决定根据长度找出大拇指的外边（长度为唯一的 6 ）和手掌的底边（长度为唯一的 9 ）
* 短边(6)向长边(9)顺时针就是 **左手**
* 短边(6)向长边(9)逆时针就是 **右手**
* 夹角一定是直角
* 这两个长度是唯一的

![示意图](/images/20200718/js.png)

接下来还有一个 **坑** 就是浮点数有误差，直接转整型也不准，所以找这个直角的三个点要通过求 **最小差值** 来判断。<br>
因为是**直角**，所以将 **角** 放到原点（转换两个点的坐标）之后，通过一堆 if 来判断角的**两个顶点**的**象限**即可，这里贴一张越越做的表<br>
![象限对应表](/images/20200718/xx.jpg)

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

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        hip t; cin >> t; while (t--) {
                //存坐标
                double p[23][2];
                for (hip i = 0; i < 20; i++) cin >> p[i][0] >> p[i][1];
                //找69
                hip li=0, lj=0, ji=0, jj=0;
                double ml=66666, mj=66666;
                for (hip i = 0; i < 20; i++) {
                        for (hip j = 0; j < 20; j++) {
                                if (i == j) continue;
                                double d = (p[i][0] - p[j][0])*(p[i][0] - p[j][0]) + (p[i][1] - p[j][1])*(p[i][1] - p[j][1]);
                                if (ml > (d-36)*(d-36)) ml = (d-36)*(d-36), li = i, lj = j;
                                if (mj > (d-81)*(d-81)) mj = (d-81)*(d-81), ji = i, jj = j;
                        }
                }
                //li - 6, lj - 9, ji - 公共
                if (li == ji) {
                        li = lj;
                        lj = jj;
                } else if (li == jj) {
                        li = lj;
                        lj = ji;
                        ji = jj;
                } else if (lj == ji) {
                        lj = jj;
                } else if (lj == jj) {
                        lj = ji;
                        ji = jj;
                }
                //处理成公共点为(0, 0)的状态
                p[li][0] -= p[ji][0], p[lj][0] -= p[ji][0];
                p[li][1] -= p[ji][1], p[lj][1] -= p[ji][1];
                //判断位置
                bool f = false;
                if (p[li][0] == 0) {
                        if (p[li][1] > 0) {
                                if (p[lj][0] > 0) f = true;
                        } else {
                                if (p[lj][0] < 0) f = true;
                        }
                } else if (p[li][0] > 0) {
                        if (p[li][1] == 0) {
                                if (p[lj][1] < 0) f = true;
                        } else if (p[li][1] > 0) {
                                if (p[lj][0] > 0 && p[lj][1] < 0) f = true;
                        } else {
                                if (p[lj][0] < 0 && p[lj][1] < 0) f = true;
                        }
                } else {
                        if (p[li][1] == 0) {
                                if (p[lj][1] > 0) f = true;
                        } else if (p[li][1] > 0) {
                                if (p[lj][0] > 0 && p[lj][1] > 0) f = true;
                        } else {
                                if (p[lj][0] < 0 && p[lj][1] > 0) f = true;
                        }
                }
                if (f) cout << "right" << endl;
                else cout << "left" << endl;
                //cout << li << ' ' << lj << ' ' << ji << endl;
        }
        return 0;
}
```
