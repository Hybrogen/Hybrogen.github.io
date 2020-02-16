---
layout: post
title: H_On Template - H_On 自制程序模板推荐
date: 2020-02-16
author: H_On
description: HT-H_On 模板【ps：希望有朝一日能出一个HTL（H_On 标准模板库）呢 嘿嘿
tag: 算法
---

### 起源
要说起猹开发这些模板的原因呢~一个是大家也知道，猹这个寒假更新了STL的介绍，两篇！可以说是人生第一次经历了大量练习获得了很多知识<br>
![学到虚脱](/images/20200216/tired.jpg)<br>
要说直接原因，就是昨天打 CF 的时候 sages7 给我推荐了一个好用的 cf 小工具，可以自动爬取比赛题对外的测试数据，还可以用命令测试提交代码。然后它支持在生成的题目文件夹里生成一个编程代码模板，这个是自己给定的。正好我早就想优化一下我的那些乱七八糟的代码了，于是趁此机会，整活一天终于获得了一些收获，现在迫不及待的开始写分享博客了owo

其实我想优化代码的想法很久了，不知道大家有没有遇到这种情况，有时候会经常时不时的就需要写一些比较结构体，用来让 set 按降序排列，可是有时候是排 int 元素有时候是 long long 还有时候甚至是 string ，那么就算把比较结构体的大框架留下来，每次使用都可能需要修改一堆变量类型名，由于 STL 那些容器都可以随意的设置元素的变量类型，所以我也想整一个全类型通用比较结构体。<br>
实际上以前什么两数交换啊，求两个数的最小公倍数这两个函数，猹也都是憨憨每次都自己手写，也经常因为变量类型不通用而烦恼。于是这次我找了好多网页，甚至查阅了源代码，最终初窥门径了！

### 先说说比较好用但其实没啥用的交换函数
这个函数是真的，很简单，但是每次对于不同的变量都要重写真的好麻烦（要改三个地方的变量类型呢），于是我先研究的这个。
```c++
template <class H_On> void EX(H_On &a, H_On &b) {
        H_On t = a;
        a = b;
        b = t;
}
```
`H_On` 的地方就是每次要交换的变量类型不一样的地方要改的，尤其是经历了昨晚我在最后一分钟交了一道题，还没判过比赛就结束了，但最后是 AC 之后，我真心感觉能节约几秒都是好的，可是总是改真的很烦啊，经历过就知道辣 > m <<br>
总而言之呢，这样子一来，我再无论怎么用 EX() 这个函数，不管丢进去什么类型的变量都可以互换辣，开心。

ps：其实 `<algorithm>` 库里有一个函数叫 `swap()` ，底层就是用这种模板类型的方式实现的，甚至对整型变量还有特异优化。这个函数也是我在 跟 sages7 讨论我的想法的时候跟我说的 ~~消磨猹探索积极性的 sages7 是屑，还好他没成功~~

### 然后是也没啥用的求最大公约数函数
这个函数的话也是时不时就会用到的，但是会遇到有时候用 int 有时候用 long long 所以其实也是有这个需求的。
```c++
template <class H_On> H_On GCD(H_On a, H_On b) {
        if (!b) return a;
        if (!a) return b;
        while (b) {
                H_On t = a % b;
                a = b;
                b = t;
        }
        return a;
}
```
跟上面那个类似只不过 还多了一个返回值，然后为什么说没用呢，因为其实 `<algorithm>` 库里还有一个函数 `__gcd()` 也是可以求最大公约数的

### 比较重要的 - 自定义比较结构体和一些常数的定义
#### STL 常备自定义比较结构体
我们在使用 set/multiset 容器的时候经常会遇到需要将默认升序排序变成降序排序的情况，而同样的，就算是我们只保留简单的换顺序的排序规则，也时常遇到变量类型要换的情况，所以这里给出了这个，同样是用模板类实现的
```c++
template <class H_On_compT> struct hon_cmp {
        bool operator () (const H_On_compT &a, const H_On_compT &b) {
                return a > b;
        }
};
```
这时候直接写 `set<T, hon_cmp<T>>` 即可，注意，要写两个变量类型而且要一样<br>
例如：`set<int, hon_cmp<int>> s;` 这样子再往集合容器 s 中插入元素时就会默认是升序排列啦。

#### 常用常数的多类型定义
同样是 int 和 long long 的问题，但是这里要注意，因为不是函数而是直接获取一个变量，或者说我需要直接获取一个数，一个固定值，所以这里我用了另一种方式，就是使用模板变量的形式然后使用类来生成一个对象，作为我们需要的变量
```c++
template <typename H_On_infnT> class infn {
        public:
                H_On_infnT N;
                infn(H_On_infnT honValue) {
                        this->N = honValue;
                }
        };
template <typename H_On_maxnT> class maxn {
        public:
                H_On_maxnT N;
                maxn(H_On_maxnT honValue) {
                        this->N = honValue;
                }
        };
```
这里要简单的介绍一下使用方法，有点不太好探索
```c++
infn<int> INF(int(1e9 + 9));
maxn<string> MAX("nihao");
cout << "inf = " << INF.N << endl;
cout << "max = " << MAX.N << endl;
```
输出就长这样
```
inf = 1000000009
max = nihao
```
如你所见，支持全类型的特殊功能定义。

### 闲言碎语
最后还是猹的无聊自言自语，猹其实是喜欢把所有常用的东西都写上，但是为了明确我用到了哪那个功能，会把所有的东西都尽量写成一行，然后用到哪行把哪行注释掉，如下所示是猹完成的模板
```c++
#include <iostream>
using namespace std;
//#include <cmath>
//#include <set>
//#include <map>        //map<int, int, greater<int>> m;
//#include <stack>
//#include <vector>
//#include <deque>
//#include <bitset>
//#include <algorithm>  //__gcd(), swap()
//#include <iomanip>
//#define dfos(outStep) fixed << setprecision(outStep)

//typedef long long ll;

//template <typename H_On_infnT> class infn {public: H_On_infnT N; infn(H_On_infnT honValue) {this->N = honValue;}};
//template <typename H_On_maxnT> class maxn {public: H_On_maxnT N; maxn(H_On_maxnT honValue) {this->N = honValue;}};
//template <class H_On_compT> struct hon_cmp {bool operator () (const H_On_compT &a, const H_On_compT &b) {return a > b;}};

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        int t; cin >> t;
        while (t--) {

        }
        return 0;
}
```
可以看到，全部都一行注释掉，还会加一些暂时不熟练的用法或者函数的注释，然后用到那个函数库或者预定义就取消掉注释，实在不喜欢有些人提交的代码有很多没用的东西，当然这只是个人看法。没准有一天猹也会因为觉得麻烦而提交一大堆没用的代码，谁知道呢 =w=<br>
最主要的是希望各位如果还是比较新手的阶段，不要对知识不求甚解，仅仅知道用法不知道原理，这样是不对的。

今天一整天，为了做这个查了好多人家的文章，还翻了几个小时的底层实现 vector 啊什么的容器的源代码，总算是搞清了一点点 **“模板”** 的工作原理，总归做出来了还是挺开心的

最后祝大家学习快乐，天天进步 挥挥~

/ *本站内容未经博主（H_On）同意严禁转载* /
