---
layout: post
title: HDU-1540 Tunnel Warfare 题解 线段树特殊储存/set 集合容器红黑树的应用
date: 2020-03-21
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[Tunnel Warfare](http://acm.hdu.edu.cn/showproblem.php?pid=1540)

### 题目
![题面](/images/20200331/Q.png)

### 题意
地~道战 嘿！地~道战<br>
现在有一条地道，告诉你有 n 个出口，沿着地道是 1，2，3，，n 依次排列的<br>
然后有 m 个操作：
* `D x` 表示摧毁一个出口 x，摧毁之后 x 这个出口两边的地道不再相连
* `Q x` 是访问于 x 这个出口相连的能用的出口总共有几个，包括 x
* `R` 将上一个被摧毁的出口重建

### 解题思路
法一：从题意来看，就是一个单点更新和区间求和的题，但问题是如何来求 `与这个点相连的能用的点` 百思不得其解之后看博客去搞懂了<br>
这个巧解的做法是，储存这个节点所管理的区间里
* 从最左端往右有几个能用的点
* 从最右端往左有几个能用的点
* 整个区间里的几段能用的点，最长的一段有多长

法二：储存`被摧毁`的点，被摧毁的点`中间的长度`就是`未摧毁的区间`，直接使用 `set 集合容器`的`自动排序`和 `upper_bound() 方法` 进行计算

代码注释详解

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
#include <stack>
const hip maxn = int(5e4 + 9);
#define L t << 1
#define R t << 1 | 1

//定义节点结构体
//l - 左边界 r - 右边界
//m - 中间值 w - 区间长度
//a - 从左边开始的最大长度
//b - 整个区间中的最大长度
//c - 从右边开始的最大长度
struct P {hip l, r, m, w, a, b, c;} tt[maxn << 2];

//建树
void build(hip l, hip r, hip t) {
        hip w = r - l + 1;
        tt[t] = {l, r, (l + r) >> 1, w, w, w, w};
        if (l == r) return ;
        build(l, tt[t].m, L);
        build(tt[t].m + 1, r, R);
}

//更新，只能到叶子节点才更新，但是
//更新完子节点以后对于父节点的更新有点讲究
void update(hip p, hip n, hip t) {
        //超界直接返回
        if (p < tt[t].l || p > tt[t].r) return ;
        //叶子节点直接更新
        if (tt[t].l == tt[t].r) {tt[t].a = tt[t].b = tt[t].c = n; return ;}
        update(p, n, L); update(p, n, R);
        //更新区间内最长区间的长度 max(左子区间里的最长，右子区间里的最长，中间连接的长度)
        tt[t].b = max(max(tt[L].b, tt[R].b), tt[L].c + tt[R].a);
        //左最长就等于左子区间的左最长，如果连起来就加上右子区间的左最长
        tt[t].a = tt[L].a; if (tt[t].a == tt[L].w) tt[t].a += tt[R].a;
        //右最长就等于右子区间的右最长，如果连起来就加上左子区间的右最长
        tt[t].c = tt[R].c; if (tt[t].c == tt[R].w) tt[t].c += tt[L].c;
}

//更新
hip query(hip p, hip t) {
        //如果已经访问到叶子节点直接返回
        if (tt[t].l == tt[t].r) return tt[t].b;
        //如果请求点在中间连续区间的左边，往左边找
        if (p <= tt[t].m - tt[L].c) return query(p, L);
        //如果请求点在中间连续区间的右边，往右边找
        if (p > tt[t].m + tt[R].a) return query(p, R);
        //如果就在中间连续的区间中，就返回这个长度
        return tt[L].c + tt[R].a;
}

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        //记得多实例
        hip n, m; while (cin >> n >> m) {
                //建树
                build(1, n, 1);
                //用栈容器来储存摧毁/修复记录
                stack<hip> s;
                while (m--) {
                        char o; cin >> o;
                        if (o == 'D') {hip p; cin >> p; update(p, 0, 1); s.push(p);}
                        if (o == 'Q') {hip p; cin >> p; cout << query(p, 1) << endl;}
                        if (o == 'R') update(s.top(), 1, 1), s.pop();
                }
        }
        return 0;
}
```

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
#include <set>
#include <stack>

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        //记得多实例
        hip n, m; while (cin >> n >> m) {
                //用栈容器来储存摧毁/修复记录
                stack<hip> s;
                //用 set 集合容器来储存被摧毁的点
                set<hip> e;
                //为了防止请求到两端的区间只有一个边界
                //加两个边界数作为边界，方便计算操作
                e.insert(0); e.insert(n + 1);
                while (m--) {
                        char o; cin >> o;
                        if (o == 'D') {hip p; cin >> p; e.insert(p); s.push(p);}
                        if (o == 'Q') {
                                hip p; cin >> p;
                                //如果请求的点在容器内，即已经被摧毁
                                //直接输出 0
                                if (e.find(p) != e.end()) {cout << 0 << endl; continue;}
                                //从集合中找到一个大于请求的数的数
                                //减去上一个数即为请求点所在区间
                                auto a = e.upper_bound(p), b = a;
                                //两个被摧毁的点的数值相减-1就是包含请求点的没被摧毁的连续点数
                                cout << *a - *--b - 1 << endl;
                        }
                        if (o == 'R') e.erase(s.top()), s.pop();
                }
        }
        return 0;
}
```

### 参考文章
[HDU-1540 Tunnel Warfare 线段树最大连续区间 或 STL巧解](https://blog.csdn.net/weixin_44077863/article/details/99681125)
