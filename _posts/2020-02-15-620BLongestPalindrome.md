---
layout: post
title: 【Codeforces Round#620 (Div. 2)】B. Longest Palindrome 题解
date: 2020-02-15
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[B. Longest Palindrome](https://codeforces.com/contest/1304/problem/B)

### 题目
Returning back to problem solving, Gildong is now studying about palindromes. He learned that a palindrome is a string that is the same as its reverse. For example, strings "pop", "noon", "x", and "kkkkkk" are palindromes, while strings "moon", "tv", and "abab" are not. **An empty string is also a palindrome.**

Gildong loves this concept so much, so he wants to play with it. He has **n** distinct strings of equal length **m**. He wants to discard some of the strings (possibly none or all) and reorder the remaining strings so that the concatenation becomes a palindrome. He also wants the palindrome to be as long as possible. Please help him find one.

### 输入
The first line contains two integers **n** and **m (1≤n≤100, 1≤m≤50)** — the number of strings and the length of each string.

Next **n** lines contain a string of length **m** each, consisting of lowercase Latin letters only. All strings are distinct.

### 输出
In the first line, print the length of the longest palindrome string you made.

In the second line, print that palindrome. If there are multiple answers, print any one of them. If the palindrome is empty, print an empty line or don't print this line at all.

### 样例
input
```
3 3
tab
one
bat
```
output
```
6
tabbat
```
input
```
4 2
oo
ox
xo
xx
```
output
```
6
oxxxxo
```
input
```
3 5
hello
codef
orces
```
output
```
0

```
input
```
9 4
abab
baba
abcd
bcde
cdef
defg
wxyz
zyxw
ijji
```
output
```
20
ababwxyzijjizyxwbaba
```

### 题意
第一行是两个数字 字符串个数 t 字符串长度 l 。<br>
之后 t 行是都是**全不相同**的字符串，问你选其中的哪几个可以组成一个回文串。输出得到的回文串长度和得到的回文串。

### 坑
先说几个坑点：
1. 有本身就是回文串的字符串，只能作为中间串，而且最重要的是要选择**最后**一个回文串作为中间串
2. 作为两边的字符串要按顺序依次输出，顺序不能乱

虽然题里说好几种情况都算，但是【顺序】和【中间串的选择】乱的话就会错 QAQ

### 解题思路
* 用 map 标记已经用过的字符串，vector 储存所有字符串方便遍历。
* 遍历字符串的时候
  - 当前的字符串已经用过，则直接跳过
  - 这个字符串可以用的话先将这个字符串在 map 里标记一下
  - 如果这个字符串是回文串，则作为最中间的串，中间串标记 mc 置 1 【取最后一个回文串做中间串
  - 如果这个字符串的反串存在，把反串也在 map 里标记，那么就把这个字符串存起来，把 字符串读取数量 加 2 【加到保存字符串后面
* 最后输出字符串长度 `l*(mc + rc)` 和保存字符串、中间字符串、保存字符串的反串。

### 代码
```c++
#include <iostream>
#include <map>
#include <vector>
#include <algorithm>

using namespace std;

bool IR(string s) {string a = s; reverse(a.begin(), a.end()); return a == s;}

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        int t; cin >> t;
        int l; cin >> l;
        map<string, int> d;
        vector<string> v;
        while (t--) {
                string a; cin >> a;
                d[a] = 1; v.push_back(a);
        }
        string s, m;
        int mc = 0, rc = 0;
        for (auto a : v) {
                if (!d[a]) continue;
                d[a] = 0;
                if (IR(a)) m = a, mc = 1;
                else {
                        reverse(a.begin(), a.end());
                        if (d[a]) reverse(a.begin(), a.end()), s += a, d[a] = 0, rc += 2;
                }
        }
        cout << l*(mc + rc) << endl;
        cout << s << m; reverse(s.begin(), s.end()); cout << s << endl;
        return 0;
}
```
