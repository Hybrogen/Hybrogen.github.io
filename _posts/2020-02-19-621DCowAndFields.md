---
layout: post
title: 【Codeforces Round#621 (Div. 1 + Div. 2)】D. Cow and Fields 题解 - 最短路
date: 2020-02-19
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[D. Cow and Fields](https://codeforces.com/contest/1307/problem/D)

### 题目
一直觉得英文题题面很难理解，然后也有题目链接，很纠结要不要每次都把题目再写一遍，昨天看大佬题解是贴一个截图上去，感觉也不错，以后我就这么干辣

![D 题题面 1](/images/20200219/D1.png)
![D 题题面 2](/images/20200219/D2.png)

### 题意
给你 n 个点，t 条边，k 个特殊点。问你如何给这个图加一条连结两个特殊点的边，使新图点 1 到点 n 的 **最短路最长**。<br>
问你最长的最短路的值。（好绕啊）

### 解题思路
参考这位大佬的[题解](https://blog.csdn.net/qq_43627087/article/details/104379980)<br>
1. 先储存所有的特殊点，因为结果只是求路长，所以点的坐标存成 `从 0 开始` 无所谓，从 0 开始操作起来比较方便
2. 接下来储存边，同样是使点的标号从 0 开始，使用 map 构建**邻接矩阵**节约空间并且方便之后查询
3. 找出 `点 1 到每个点的最短距离 la` 和 `点 n 到每个点的最短距离 lb`
   1. 搞一个标记距离的数组并初始化成无穷大，将距离 1 （或 n）的距离设置成 0
   2. 创建一个队列用于更新更新距离，将初始点入队
   3. 广搜思路更新所有的点到 1 （或 n）的距离，返回数组
4. 按距离 1 点递增，距离 n 点递减的顺序将所有特殊点排序
5. 最后一块是求出：点 1 到`靠近 1 的跳跃点`的距离 + 点 n 到`靠近 n 的跳跃点`的距离
   * l 表示 **不跳过的总长度** ，即：点 1 到靠近 1 的跳跃点的距离 + 点 n 到靠近 n 的跳跃点的距离
   * ma 表示第一个跳转点到点 1 的距离

   因为两个连接点不能为同一个点，因此 `l = max(l, ma + lb[t])` 的时候的 **ma** 应该是上一个点得出的，所以要先求出 **l** 再求出下一个 **ma** 。为了保证第一次循环 **l** 依然是 **0** ，**ma** 的初始值应该小于等于 **-n**
6. 最后结果取 `原图最短路` 和 `连线之后的最大最短路` 之间的最小值（有可能两个特殊点之间本来就有连线，此时不应再加一）

### 代码
```cpp
#include <iostream>
using namespace std;
#include <map>
#include <vector>
#include <queue>

template <typename H_On_infnT> class infn {public: H_On_infnT N; infn(H_On_infnT honValue) {this->N = honValue;}};
infn<int> INF(int(2e5 + 9));

vector<int> bfs(int initn, int n, map<int, vector<int>> m) {
	vector<int> v; for (int i = 0; i < n; i++) v.push_back(INF.N);
	v[initn] = 0;
	queue<int> q; q.push(initn);
	while (!q.empty()) {
		int z = q.front(); q.pop();
		for (int a : m[z]) if (v[a] == INF.N) {
			v[a] = v[z] + 1;
			q.push(a);
		}
	}
	return v;
}

int main() {
	ios::sync_with_stdio(0); cin.tie(0);
	int n, t, k; cin >> n >> t >> k;

	//储存特殊点
	vector<int> sp;
	while (k--) {int a; cin >> a; sp.push_back(a - 1);}

	//存边
	map<int, vector<int>> m;
	while (t--) {
		int a, b; cin >> a >> b;
		a -= 1; b -= 1;
		m[a].push_back(b);
		m[b].push_back(a);
	}

	//标记数组
	vector<int> la = bfs(0, n, m), lb = bfs(n - 1, n, m);

	//按距离1点由近到远所有特数点排序
	multimap<int, int> v;
	for (int i : sp) v.insert(pair<int, int>(la[i] - lb[i], i));

	//求所有连接方式中最短路的最大值
	int l = 0, ma = -n;
	for (auto a : v) {
		t = a.second;
		l = max(l, ma + lb[t]);
		ma = max(ma, la[t]);
	}
	int r = min(la[n - 1], l + 1);
	cout << r << endl;
        return 0;
}
```
