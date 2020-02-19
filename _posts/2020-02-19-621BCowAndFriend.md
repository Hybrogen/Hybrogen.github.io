---
layout: post
title: 【Codeforces Round#621 (Div. 1 + Div. 2)】B. Cow and Friend 题解
date: 2020-02-19
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[B. Cow and Friend](https://codeforces.com/contest/1307/problem/B)

### 题目
一直觉得英文题题面很难理解，然后也有题目链接，很纠结要不要每次都把题目再写一遍，昨天看大佬题解是贴一个截图上去，感觉也不错，以后我就这么干辣

![B 题题面 1](/images/20200219/B1.png)
![B 题题面 2](/images/20200219/B2.png)

### 题意
有 n 种步长，规定每一步只能走给定的步长，问你从 (0, 0) 走到 (x, 0) 需要几步

### 解题思路
如果我最长的步长大于 x/2 ，那么说 我最多走两步肯定可以走到 x ，就是走一个 **等腰三角形**，很好理解叭。<br>
最终的思路是，在输入的时候用 map 记录步长，用 ma 记录最大步长
1. 如果一步就能走到，那就是 1 【防止 x 这个长度在所有步长中且不是最大步长
2. 除去上面那种情况，如果最大步长大于 x 那么最少步数就是组成一个等边三角形
3. 最后的情况就是最大步长还小于 x ，那么最少步数就是 `x/ma`
   * 如果 x 不能被 ma 整除，结果还要再加一

一开始我的 AC 代码是写了一堆判断，如果上面那种思路不好理解可以先尝试这个把情况分的比较细的思路，代码下面也有
1. 先是判断 x 在不在可选步长里面，如果一步就能走到那就输出 1
2. 如果 `x/2` 和 `x - x/2` 都在可选步长里，那就直接输出 2
3. 如果最长步长大于 x/2 那就可以组成等腰三角形，输出 2
4. 其他的情况就是输出 x 除以最大步长 `x/ma` 如果不能整除输出的值就要加 1

### 代码
```cpp
#include <iostream>
using namespace std;
#include <map>

int main() {
	ios::sync_with_stdio(0); cin.tie(0);
	int t; cin >> t;
	while (t--) {
		int n, x; cin >> n >> x;
		int ma = 0;
		map<int, int> b;
		while (n--) {int a; cin >> a; ma = max(ma, a); b[a] = 1;}
		if (b[x]) cout << 1 << endl;
		else if (ma > x) cout << 2 << endl;
		else cout << (x % ma ? x/ma + 1 : x/ma) << endl;
	}
        return 0;
}
```

```cpp
#include <iostream>
using namespace std;
#include <map>

typedef long long ll;

int main() {
	ios::sync_with_stdio(0); cin.tie(0);
	int t; cin >> t;
	while (t--) {
		ll n, m; cin >> n >> m;
		ll ma = 0;
		map<ll, ll> b;
		while (n--) {ll a; cin >> a; ma = max(ma, a); b[a] += 1;}
		if (b[m]) cout << 1 << endl;
		else {
			if (m % 2 == 0) {
				if (b[m/2]) cout << 2 << endl;
				else {
					if (ma > m/2) cout << 2 << endl;
					else {
						if (m % ma) cout << m/ma + 1 << endl;
						else cout << m/ma << endl;
					}
				}
			} else {
				if (b[m/2] && b[m/2 + 1]) cout << 2 << endl;
				else {
					if (ma > m/2) cout << 2 << endl;
					else {
						if (m % ma) cout << m/ma + 1 << endl;
						else cout << m/ma << endl;
					}
				}
			}
		}
	}
        return 0;
}
```
