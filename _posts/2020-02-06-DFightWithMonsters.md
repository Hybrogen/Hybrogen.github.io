---
layout: post
title: 【寒假CodeForces】D. Fight with Monsters 题解
date: 2020-02-06
author: H_On
description: 英文题真难理解题意
tag: 题解
---

题目链接：[D. Fight with Monsters](https://codeforces.com/contest/1296/problem/D)

### 题目
There are **n** monsters standing in a row numbered from **1** to **n**. The **i-th** monster has **hi** health points (hp). You have your attack power equal to **a** hp and your opponent has his attack power equal to **b** hp.

You and your opponent are fighting these monsters. Firstly, you and your opponent go to the first monster and fight it till his death, then you and your opponent go the second monster and fight it till his death, and so on. A monster is considered dead if its hp is less than or equal to **0**.

The fight with a monster happens in turns.
1. You hit the monster by **a** hp. If it is dead after your hit, **you gain one point** and you both proceed to the next monster.
2. Your opponent hits the monster by **b** hp. If it is dead after his hit, **nobody gains a point** and you both proceed to the next monster.

You have some secret technique to force your opponent to skip his turn. You can use this technique at most **k** times in total (for example, if there are two monsters and **k=4**, then you can use the technique **2** times on the first monster and **1** time on the second monster, but not **2** times on the first monster and **3** times on the second monster).

Your task is to determine the maximum number of points you can gain if you use the secret technique optimally.

### 输入
The first line of the input contains four integers **n,a,b** and **k (1≤n≤2⋅10^5,1≤a,b,k≤10^9)** — the number of monsters, your attack power, the opponent's attack power and the number of times you can use the secret technique.

The second line of the input contains **n** integers **h1,h2,…,hn (1≤hi≤10^9)**, where **hi** is the health points of the **i-th** monster.

### 输出
Print one integer — the maximum number of points you can gain if you use the secret technique optimally.

### 样例输入
```
6 2 3 3
7 10 50 12 1 8
```
```
1 1 100 99
100
```
```
7 4 2 1
1 3 5 4 2 7 6
```

### 样例输出
```
5
```
```
1
```
```
6
```

### 题意
第一行输入 4 个整数
* n - 怪兽的数量
* a - 你的攻击力
* b - 对手的攻击力
* k - 你可以控制对手跳过多少个回合

你和对手轮流对怪物造成伤害，你先手，你可以控制对手跳过 k 个回合，求最多有多少怪物能被你打败。

### 解题思路
先把杀死每一个怪物所需要的 **使对手跳过回合的机会的个数** 都存起来并升序排序，然后依次遍历，消耗机会来杀死怪物，机会消耗完了以后，杀死的怪物数量就是最多的个数。升序排序可以用数组或者 vector 向量容器储存，然后再 sort 排序；或者像猹这样用 multiset 多重集合容器自动排序，最后直接遍历即可。<br>
那么怎么把【杀死怪物需要的机会】给算出来呢，可以用 **对两人攻击力和取余** 的方式获得怪物在经历回合之后剩余的血量来跟两者的攻击力进行比对，由我杀死怪物有两种情况
* 如果怪物的血量 hi 对 (a + b) 取余剩下的值小于我的攻击力 a ，那么我先打它一下就行了，所以不需要使用机会，最后一击一定是我的
* 如果 hi % (a + b) 为 0 或者大于我的攻击力的话，那我就打到它的血量变成满足上面那种情况就行了

这边的思路就是先求出需要我打掉的血量，然后 **【我的攻击次数减一】** 就是我需要使用的机会个数。这是一个普适用的计算方法。
* **s** 是怪物的血量
* **t = s/(a + b)** 是求出两个人都满输出攻击，能进行几个回合
* 如果 **s** 能被 **a + b** 取余，那么就将 **t - 1** ，留出来一个给我操作
* 然后把怪物的血量 **s** 减去全输出攻击完整回合输出攻击量
* 如果 **s** 不能被 **a** 整除，那就使用 **s/a** 次机会让我攻击 **s/a + 1** 次（因为整数相除向下取整）
* 最后如果 **s** 是 **a** 的整倍数，那就使用 **s/a - 1** 次机会让我攻击 **s/a** 次

最后遍历升序排列的次数，依次使用次数 k 来击杀怪物，机会用完或者怪物杀完了就停止，获得杀怪数

ps：那个多重集合容器 v 的遍历不理解的可以去看一看猹的 STL进阶 博客 [STL Plus](https://hybrogen.github.io/2020/02/STL2/)

### 代码
```c++
#include <iostream>
#include <set>

using namespace std;

multiset<int> v;

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        int n, a, b, k; cin >> n >> a >> b >> k;
        while (n--) {
                int s; cin >> s;
                int t = s/(a + b);
                if (s % (a + b) == 0) t -= 1;
                s -= t*(a + b);
                if (s % a) s = s/a; else s = s/a - 1;
                v.insert(s);
        }
        int r = 0;
        for (int t : v) if (k >= t) r += 1, k -= t; else break;
        cout << r << endl;
        return 0;
}
```
