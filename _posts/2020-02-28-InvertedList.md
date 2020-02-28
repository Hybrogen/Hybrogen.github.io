---
layout: post
title: 【一败涂地】1025 反转链表 (25分)
date: 2020-02-28
author: H_On
description: 我真的是，25分以上的都是什么垃圾题
tag: 题解
---

题目链接：[1025 反转链表 (25分)](https://pintia.cn/problem-sets/994805260223102976/problems/994805296180871168)

### 题目
![题目](/images/20200228/il.png)

### 解题思路
> 先心态爆炸一会
> * 这题真的把我心态弄得特别崩了。一开始用 `map<string, pair<int, string>>` 存原数据，vector 存个按顺序的地址，不行，样例过了一直不过。
> * 后来有~~我就不说了的~~博客说有可能有的节点不在链表里。。。我就信了，然后加了个 set 最后遍历一遍输出没有在链表里的节点。
> * 我复制一个莫名其妙的代码，AC 了，从此忘了爱，做别的题不香吗？？？
> * 能做的题都做完了，我回来了，我是来谈条件的！从头写，哈哈哈，真快乐。欸吖错了，错了错了错了错了错了，啊啊啊啊啊啊啊
> * 差点就被心魔夺取了理智，冷静。哦原来要 `每 K 个结点` 都反转啊 - 嘟 - 嘟 - 嘟 - 我好了 submit ~ TLE ？？？
> * 于是又去网上找别人的博客，怎么感觉还是看不懂。绝望到最后，决定直接按顺序存到 vector 里然后一段一段反转好了。。。

我过了，莫名其妙的过了
1. 只要从头结点开始遍历，按顺序存进 `vector`
2. 然后跳跃遍历，把能反转的段都用 `<algorithm> 里的 recerse() 函数` 反转一下
3. 不用考虑什么 `不在链表上的点` 反转完了按顺序输出就行了

注意：
* 输出的时候，不能用节点的 `next` 因为顺序打乱了以后结构体里的顺序并没有变
* 所以输出的地址是 `当前地址` ；数据是 `当前数据` ；next 地址是 `下一个节点的当前地址`
* 总之就是先输出第一个节点的 `地址和数据` 然后循环输出 `地址，回车，地址和数据，不回车` 最后输出 `-1，回车`

### 代码
```c++
#include <iostream>
using namespace std;
#include <map>
#include <vector>
#include <algorithm>

struct P {
        int d;
        string t, n;
};

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        string b; int s, k;
        cin >> b >> s >> k;
        map<string, P> m;
        for (int i = 1; i <= s; i++) {
                string t, n; int d;
                cin >> t >> d >> n;
                P p = {d, t, n};
                m[t] = p;
        }
        int l = 0;
        vector<P> v;
        while (b != "-1") v.push_back(m[b]), b = m[b].n, l += 1;
        for (int i = 0; i + k <= l; i += k)
                reverse(v.begin() + i, v.begin() + (i + k));

        cout << v[0].t << ' ' <<  v[0].d << ' ';
        for (int i = 1; i < l; i += 1)
                cout << v[i].t << endl << v[i].t << ' ' << v[i].d << ' ';
        cout << "-1" << endl;
        return 0;
}
```
