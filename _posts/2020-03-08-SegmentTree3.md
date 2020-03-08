---
layout: post
title: 线段树学习总结 Part 3 - 离散化
date: 2020-03-08
description: 线段树 + 离散化
tag: 算法
---

本文是关于 **线段树 + 离散化** 的讲解
* 如果你对线段树完全不了解，建树时有一些疑惑，看本文有点不理解，可以试试先看一下 [线段树的建树方法与原理](http://blog.zinchon.cn/2019/09/SegmentTree/)
* 如果你已经对于线段树的基础操作（单点/区间修改，查询）还不太了解，请观看 [线段树的一些基础操作](http://blog.zinchon.cn/2019/09/SegmentTree2/)

### 被迫营业
最近这一段时间，嘛~做了一些事情，虽然都在原计划内，但开始的原因还是有点被迫开始的意思。<br>
借口一个被迫之名激励自己做事情，对我来说是挺受用的 =w= ~~我可不是什么变态啊~~

### 目录
* [离散化的意义](#离散化的意义)
* [离散化的方法](#离散化的方法)
* [线段树的操作](#线段树的操作)
* [参考文章](#参考文章)
* [线段树 + 离散化例题](#线段树-+-离散化例题)
* [例题完整代码](#例题完整代码)
* [基础线段树模板](#基础线段树模板)

### 离散化的意义
比较了解猹的人会发现一个事情，就是猹在介绍一些东西的时候，总会尝试吧自己认为很难理解，很重要，或是网上很难找到的方面和知识点。详细的，尽量清楚的揭示出来，好让自己以为这样可以使尽可能少的人再走猹的老弯路。<br>
因此这里想要先说一下离散化和学习离散化的意义。

在做了好多比赛之后就会发现，线段树作为一个强力的工具，好像并不是那么好用。<br>
即使你理解了前两篇文章的内容，你会发现。。。这玩意有啥用啊？？？好像除了线段树模板题，别的情景都用不到啊。<br>
我认为，这种情况就是因为线段树的基础是一个数据结构，是一个树形管理区间的思想，实际的应用还需要学很多扩展的知识，呃~由于猹能力有限，现在先小小的扩展一下`离散化`。

![离散化的概念](/images/20200308/st3-concept.png)<br>
图片来源于：百度百科

如图所示，离散化大概就是能把树从 `管理 1 - 1000 这 1000 个数` 变成 `管理 1，2 这两个数` ，那么它有什么用的<br>
就像之前的模板题[敌兵布阵 HDU-1166](http://acm.hdu.edu.cn/showproblem.php?pid=1166)是让你管理一个范围内的 `人数` ，现在我让你管理一个范围内的 `帐篷数` 233，是不是有点懵，那么接下来我们看一道经典的 `线段树 + 离散化` 的题吧

首先了解一下这道题目：[Mayor's posters POJ-2528](http://poj.org/problem?id=2528)<br>
这道题叫“市长的海豹们”，大概题意就是让你按顺序贴海报，问你最后人们能看到几张不同的海报<br>
题目上说了海报的长度（数据范围）是 `1 <= li <= ri <= 10000000(1e7)` 那么想直接维护这么大的区间的话，这个线段树的大小就是 `4e7` 单单是建树就要 `4e7*log(4e7) ≈ 1e9` （ps：1e8 的时间复杂度大概是 1s）所以说直接建树的话，但是建树就要 10s 更别说之后的操作了。。。<br>
因此我们要利用**离散化：将有限的数据放在优先的空间里**。因为我们会发现，海报的数量最多也就 `10000` 个，我们要考虑把海报的两边存起来也就 `2e4` 的数据量，建树是 `8e4` 因此离散化是优化时间和空间的好方法

### 离散化的方法
离散化的理解正如百度百科的样例一样：把 `1, 999, 100000, 15` 变成 `1, 3, 4, 2`<br>
我们需要确定各个数字在这一堆数里的**位置**，因此需要两步操作：**排序 和 去重**<br>
用到两个 `<algorithm>库` 里的函数 `sort()` 、 `unique()` 和 `upper_bound()`
1. 首先输入一个 `n` 表示有 `n 张海报`
2. 将数据存入原数据数组 `orig` 和离散数据数组 `bcre` 这里强烈建议大家使用 `vector 容器` 来代替数组，同样可以预定数组大小，和传统数组一样，而且操作更灵活
3. 将 `n*2` 方便之后使用，因为我们储存了海报的两边，因此数组里的元素一定是 `2n` 个
4. `sort()` 用于将数组区间内的元素排序
5. `unique()` 用于将数组区间内 `相邻的相同元素` 进行删除只剩下一个，排序后再这样删除就是 `去重` 辣
6. 做完去重的工作以后再遍历原数组，使用 `upper_bound()` 函数求出原数据中每一个数在所有数里 `排第几`
   - `upper_bound()` 返回指定数组区间中出现的`第一个`大于`指定数`的数的位置
   - 我们用这个位置减去开始位置 `bcre.begin()` 就能求出包括这个数在内前面有多少个数了
   > 由于这个**原数据中的数**一定存在于**去重之后的数据**中，因此这里也可以换成
   > ```cpp
   > orig[i] = lower_bound(bcre.begin(), bcre.end(), orig[i]) - bcre.begin() + 1;
   > ```
   > `lower_bound()` 返回指定数组区间中出现的`第一个`大于等于`指定数`的数的位置

```cpp
//这里强烈建议大家，使用 vector 容器来代替数组
typedef int hip;
vector<hip> orig, bcre;
hip n; cin >> n;
//2. 保存数据
for (hip i = 1; i <= n; i++) {
	hip a, b; cin >> a >> b;
	orig.push_back(a);
	orig.push_back(b);
	bcre.push_back(a);
	bcre.push_back(b);
} n <<= 1;
//4. 5. 6. 离散化
sort(bcre.begin(), bcre.end());
unique(bcre.begin(), bcre.end());
for (hip i = 0; i < n; i += 1)
	orig[i] = upper_bound(bcre.begin(), bcre.end(), orig[i]) - bcre.begin();
        //orig[i] = lower_bound(bcre.begin(), bcre.end(), orig[i]) - bcre.begin() + 1;
```
如此一来，离散化的工作就做完了，也解释清楚了。

---

由于猹喜欢极尽全力的滥用 STL ，因此这里简单说一个别的方法，其实区别差不多，只是这个排序加去重。。。听着不熟悉吗<br>
然鹅因为集合容器不能直接使用 `lower_bound() 或者 upper_bound(）` 所以还弄个向量容器转存一下，最坑的是 poj 不支持高级 for 循环，只能用迭代器遍历，总的来说，得不偿失，大家看情况吧
```cpp
typedef int hip;
vector<hip> orig, acre;
set<hip> bcre;
hip n; cin >> n;
for (hip i = 1; i <= n; i++) {
        hip a, b; cin >> a >> b;
        orig.push_back(a);
        orig.push_back(b);
        bcre.insert(a);
        bcre.insert(b);
} n <<= 1;
for (set<hip>::iterator i = bcre.begin(); i != bcre.end(); i++)
	acre.push_back(*i);
for (hip i = 0; i < n; i += 1)
	orig[i] = upper_bound(bcre.aegin(), acre.end(), orig[i]) - acre.begin();
```

### 线段树的操作
那么离散化完成了以后，就要考虑如何用线段树来快速处理这些数据了，如果用数组来挨个数这几万个数很显然也是不切实际的，因此仍然要用线段树来操作<br>
因为这里我们需要的是 `露出来的不同海报的个数` 所以和以前的线段树操作不太一样：
* 首先访问区间边界要明确，只能用 `==`
* 其次标记的传递只能在更新的时候用，最后请求的时候没有传递的必要
* 只需要更新一个 `标记` 就好了，这个点的标记就是这个点的值，因为是贴海报所以更新的时候直接覆盖原来的值
* 初始值是一个不存在的数，对于这道题来说可以是 `0`
* 主函数中的操作
  1. 输入数据
  2. 离散化
  3. 建树
  4. 更新线段树
  5. 求不同的值的个数
* 由于各个函数太杂乱，放在注释里面说明了，请认真看注释理解代码

发现之前的两篇好像没有贴出结构体的形式写的代码，以后有机会可能会更新吧，这里在文章最后给大家贴出了[基础线段树模板](#基础线段树模板)以供参考对照不同
```cpp
//自定义数据类型，因为有时候程序写完了才发现需要用 long long 因此一气之下直接定义成只有我自己用的独特数据类型
typedef int hip;
//定义数据量，1e4 张海报，一张海报有左右边两个数据，所以原始数据量定成 2e4
const hip maxn = hip(2e4 + 9);
//定义所以节点和右子节点
#define L t << 1
#define R t << 1 | 1
//定义一个节点的结构体，顺便定义一个作为 树 的数组 - tree
struct P {hip l, r, m, f;}tree[maxn << 2];
//建树，所有节点的值预定义为 0
void build(hip l, hip r, hip t) {
        tree[t] = {l, r, (l + r) >> 1, 0};
        if (l == r) return ;
        build(l, tree[t].m, L);
        build(tree[t].m + 1, r, R);
}
//如果此节点已经贴了海报但却需要访问子节点，就需要向下传递标记
void push_down(hip t) {
        if (tree[t].f) {
                tree[L].f = tree[t].f;
                tree[R].f = tree[t].f;
                tree[t].f = 0;
        }
}
//更新 pl - pr 这一段中间的标记为 n ，表示这一段贴上了新的海报
void update(hip pl, hip pr, hip n, hip t) {
        //如果区间完全吻合，或者已经遍历到子节点，直接标记返回
        if ((pl == tree[t].l && pr == tree[t].r) || tree[t].l == tree[t].r) {
                tree[t].f = n; return ;
        }
        //如果要访问子节点，由于子节点要更新而且可能只更新子节点的一部分，因次要将标记先向子节点传递
        push_down(t);
        //如果更新区间完全在左子区间，那就向左子节点遍历
        //如果更新区间完全在右子区间，那就向右子节点遍历
        //如果两边都有，就都遍历，注意要修改更新请求的区间
        //由于贴海报是指定的 【每个点】 都要更新，因此确定范围必须是 "=="
        if (pr <= tree[t].m) update(pl, pr, n, L);
        else if (pl > tree[t].m) update(pl, pr, n, R);
        else update(pl, tree[t].m, n, L), update(tree[t].m + 1, pr, n, R);
}
//我们要记录【不同标记】的个数，因此使用 set 集合容器直接去重
set<hip> rest;
//直接访问使用过的点的所有标记
void query(hip ql, hip qr, hip t) {
        //如果这个点有标记，直接记录标记之后就 return
        if (tree[t].f) {rest.insert(tree[t].f); return ;}
        //如果没有标记但是已经是子节点，就不应该继续向下遍历了
        if (tree[t].l == tree[t].r) return ;
        //这时候访问所有子区间
        //由于两边的子区间可能被更新的不一样，所以不应该在此处 push_down
        //同样由于精准访问因此访问子区间要修改访问请求的区间
        query(ql, tree[t].m, L);
        query(tree[t].m + 1, qr, R);
}

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        hip t; cin >> t; while (t--) {
                vector<hip> orig, bcre;
                hip n; cin >> n;
                //输入数据
                for (hip i = 1; i <= n; i++) {
                        hip a, b; cin >> a >> b;
                        orig.push_back(a);
                        orig.push_back(b);
                        bcre.push_back(a);
                        bcre.push_back(b);
                } n <<= 1;
                //离散化
                sort(bcre.begin(), bcre.end()); unique(bcre.begin(), bcre.end());
                for (hip i = 0; i < n; i += 1)
                        orig[i] = upper_bound(bcre.begin(), bcre.end(), orig[i]) - bcre.begin();
                //建树 - 更新线段树
                build(1, n, 1);
                //这里一次访问两个，访问的就是之前按顺序输入的一张张海报的左右区间，只不过是离散化之后的左右区间
                for (hip i = 1; i < n; i += 2)
                        update(orig[i - 1], orig[i], i, 1);
                //求解之前先清空这个集合容器
                rest.clear();
                //离散化以后的数据使用的就是 1 - n 所有的数据
                //因此访问"所有的点"求出所有不同的点即可
                query(1, n, 1);
                cout << rest.size() << endl;
        }
        return 0;
}
```
到此为止，线段树 + 离散化的东西就讲的差不多了

其实感觉上离散化也是一个独立的方法，并不只在线段树相关的问题上使用，希望我以后学的更多能给大家更好的解释

与君共勉

### 参考文章
[线段树专题 线段树+离散化](https://blog.csdn.net/qq_21433411/article/details/86678427)

### 线段树 + 离散化例题
[Mayor's posters POJ-2528](http://poj.org/problem?id=2528)<br>

### 例题完整代码
```cpp
#include <iostream>
using namespace std;
typedef int hip;
#include <set>
#include <algorithm>

#include <vector>
const hip maxn = hip(2e4 + 9);
#define L t << 1
#define R t << 1 | 1

struct P {hip l, r, m, f;}tree[maxn << 2];

void build(hip l, hip r, hip t) {
        tree[t] = {l, r, (l + r) >> 1, 0};
        if (l == r) return ;
        build(l, tree[t].m, L);
        build(tree[t].m + 1, r, R);
}

void push_down(hip t) {
        if (tree[t].f) {
                tree[L].f = tree[t].f;
                tree[R].f = tree[t].f;
                tree[t].f = 0;
        }
}

void update(hip pl, hip pr, hip n, hip t) {
        if ((pl == tree[t].l && pr == tree[t].r) || tree[t].l == tree[t].r) {
                tree[t].f = n; return ;
        }
        push_down(t);
        if (pr <= tree[t].m) update(pl, pr, n, L);
        else if (pl > tree[t].m) update(pl, pr, n, R);
        else update(pl, tree[t].m, n, L), update(tree[t].m + 1, pr, n, R);
}

set<hip> rest;

void query(hip ql, hip qr, hip t) {
        if (tree[t].f) {rest.insert(tree[t].f); return ;}
        if (tree[t].l == tree[t].r) return ;
        query(ql, tree[t].m, L);
        query(tree[t].m + 1, qr, R);
}

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        hip t; cin >> t; while (t--) {
                vector<hip> orig, bcre;
                hip n; cin >> n;
                for (hip i = 1; i <= n; i++) {
                        hip a, b; cin >> a >> b;
                        orig.push_back(a);
                        orig.push_back(b);
                        bcre.push_back(a);
                        bcre.push_back(b);
                } n <<= 1;
                sort(bcre.begin(), bcre.end()); unique(bcre.begin(), bcre.end());
                for (hip i = 0; i < n; i += 1) orig[i] = upper_bound(bcre.begin(), bcre.end(), orig[i]) - bcre.begin();
                build(1, n, 1);
                for (hip i = 1; i < n; i += 2) update(orig[i - 1], orig[i], i, 1);
                rest.clear();
                query(1, n, 1);
                cout << rest.size() << endl;
        }
        return 0;
}
```

### 基础线段树模板
```cpp
#include <vector>
const hip maxn = hip(1e5 + 9);
#define L t << 1
#define R t << 1 | 1

struct P {hip l, r, m, w, v, f;}tree[maxn << 2];

vector<hip> orig(maxn);

void push_up(hip t) {tree[t].v = tree[L].v + tree[R].v;}

void push_down(hip t) {
        if (tree[t].f) {
                tree[L].f += tree[t].f;
                tree[R].f += tree[t].f;
                tree[L].v += tree[t].f*tree[L].w;
                tree[R].v += tree[t].f*tree[R].w;
                tree[t].f = 0;
        }
}

void build(hip l, hip r, hip t) {
        tree[t] = {l, r, (l + r) >> 1, r - l + 1, 0, 0};
        if (l == r) {tree[t].v = orig[l]; return ;}
        build(l, tree[t].m, L);
        build(tree[t].m + 1, r, R);
        push_up(t);
}

void update(hip pl, hip pr, hip n, hip t) {
        if (pl <= tree[t].l && pr >= tree[t].r) {
                tree[t].f += n;
                tree[t].v += n*tree[t].w;
                return ;
        }
        push_down(t);
        if (pl <= tree[t].m) update(pl, pr, n, L);
        if (pr > tree[t].m) update(pl, pr, n, R);
        push_up(t);
}

hip query(hip ql, hip qr, hip t) {
        if (ql <= tree[t].l && qr >= tree[t].r) return tree[t].v;
        push_down(t);
        hip s = 0;
        if (ql <= tree[t].m) s += query(ql, qr, L);
        if (qr > tree[t].m) s += query(ql, qr, R);
        return s;
}
```
