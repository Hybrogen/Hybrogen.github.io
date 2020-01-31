---
layout: post
title: 【一败涂地】L1-043 阅览室 (20分) 题解
date: 2020-01-31
author: H_On
description: 菜题只能试
tag: 题解
---

题目链接：[L1-043 阅览室 (20分)](https://pintia.cn/problem-sets/994805046380707840/problems/994805087447138304)

### 题目
天梯图书阅览室请你编写一个简单的图书借阅统计程序。当读者借书时，管理员输入书号并按下`S`键，程序开始计时；当读者还书时，管理员输入书号并按下`E`键，程序结束计时。书号为不超过1000的正整数。当管理员将0作为书号输入时，表示一天工作结束，你的程序应输出当天的读者借书次数和平均阅读时间。

注意：由于线路偶尔会有故障，可能出现不完整的纪录，即只有`S`没有`E`，或者只有`E`没有`S`的纪录，系统应能自动忽略这种无效纪录。另外，题目保证书号是书的唯一标识，同一本书在任何时间区间内只可能被一位读者借阅。

### 输入
输入在第一行给出一个正整数N（≤10），随后给出N天的纪录。每天的纪录由若干次借阅操作组成，每次操作占一行，格式为：

`书号`（[1, 1000]内的整数） `键值`（`S`或`E`） `发生时间`（`hh:mm`，其中`hh`是[0,23]内的整数，`mm`是[0, 59]内整数）

每一天的纪录保证按时间递增的顺序给出。

### 输出
对每天的纪录，在一行中输出当天的读者借书次数和平均阅读时间（以分钟为单位的精确到个位的整数时间）。

### 样例输入
```
3
1 S 08:10
2 S 08:35
1 E 10:00
2 E 13:16
0 S 17:00
0 S 17:00
3 E 08:10
1 S 08:20
2 S 09:00
1 E 09:20
0 E 17:00
```

### 样例输出
```
2 196
0 0
1 60
```

### 解题思路
这题全是坑，题意跟你想的都不太一样，根本不需要记录一本书的所有区间，一本书**只会借出去一次**
* 只使用 **最中间的SE** 也就是说统计的时间区间是相邻的，中间不会夹杂多余的S和E【text2 卡这个
  - 如果有多个S没有遇到E时，使用最后一个S
  - 之后一遇到E就和已经存在的S数据匹配，如果没有S的记录就跳过E记录
* 最后的结果是 **四舍五入** 注意不是 **直接舍去小数** 也不是 **有小数直接进一** 而是 **四舍五入** 【text4 卡这个

看过了代码以后突然觉得自己都想了一堆什么乱七八糟的东西，

### 代码
```c++
#include <iostream>

#define MAXN int(1e3 + 9)

using namespace std;

int v[MAXN];

int main() {
        ios::sync_with_stdio(0);
        cin.tie(0);
        int t; cin >> t;
        string s;
        getline(cin, s);
        while (t--) {
                for (int i = 0; i < MAXN; i++) v[i] = -1;
                int cc = 0, ss = 0;
                while (true) {
                        getline(cin, s);
                        int x, h, m; char c;
                        sscanf(s.c_str(), "%d %c %d:%d", &x, &c, &h, &m);
                        if (!x) break;
                        m += 60*h;
                        if (c == 'S') {
                                v[x] = m;
                        } else if (v[x] >= 0) {
                                cc += 1;
                                ss += m - v[x];
                                v[x] = -1;
                        }
                }
                if (cc) cout << cc << ' ' << int(1.0*ss/cc + 0.5) << endl;
                else cout << "0 0" << endl;
        }
        return 0;
}
```
上面那个代码是用正确思路重写的，下面那个是一开始想用vector筛选，然后改改改改对的
```c++
#include <iostream>
#include <vector>

#define MAXN int(1e3 + 9)

using namespace std;

int v[MAXN][3];
vector<int> xx;

void init() {
        for (int i = 0; i < MAXN; i++) v[i][0] = v[i][1] = -1;
        xx.clear();
}

int main() {
        ios::sync_with_stdio(0);
        cin.tie(0);
        int t; cin >> t;
        string s;
        getline(cin, s);
        while (t--) {
                init();
                while (true) {
                        getline(cin, s);
                        int x, h, m; char c;
                        sscanf(s.c_str(), "%d %c %d:%d", &x, &c, &h, &m);
                        if (!x) break;
                        m += 60*h;
                        if (c == 'S') {
                                v[x][0] = m;
                        } else if (v[x][0] >= 0) {
                                v[x][1] = v[x][0];
                                v[x][2] = m;
                                xx.push_back(x);
                                v[x][0] = -1;
                        }
                }
                int l = xx.size(), cc = 0, ss = 0;
                for (int i = 0; i < l; i++) if (v[xx[i]][2] >= 0 && v[xx[i]][1] >= 0) {
                        cc += 1;
                        ss += v[xx[i]][2] - v[xx[i]][1];
                }
                if (cc) cout << cc << ' ' << int(1.0*ss/cc + 0.5) << endl;
                else cout << "0 0" << endl;
        }
        return 0;
}
```
