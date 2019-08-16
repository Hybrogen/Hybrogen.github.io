---
layout: post
title: Roundgod and Milk Tea - 2019 hdu 多校联赛
date: 2019-08-16
description: 一道神奇的算法题【指算法神奇】
tag: 题解
---

# [HDU-6667 Roundgod and Milk Tea](http://acm.hdu.edu.cn/showproblem.php?pid=6667) - 题解

### 题目描述
Roundgod is a famous milk tea lover at Nanjing University second to none. This year, he plans to conduct a milk tea festival. There will be n classes participating in this festival, where the ith class has ai students and will make bi cups of milk tea.
<br>
Roundgod wants more students to savor milk tea, so he stipulates that every student can taste at most one cup of milk tea. Moreover, a student can't drink a cup of milk tea made by his class. The problem is, what is the maximum number of students who can drink milk tea?

**H_On の 贴心翻译：**（圆形上帝？神龙？）是南京大学著名的的奶茶爱好者。这一年，他决定组织一场奶茶鉴赏节。将会有 n 个班参加这场盛典，这里面的第 i 个班会有 ai 个人和 bi 杯奶茶。组织者（谁知道这啥名字= =）希望更多的学生能 get 到一杯奶茶，所以他规定 **每个学生最多喝一杯** 而且 **不能喝自己班的奶茶** 。<br>
那么问题来了，**最多** 能有多少学生能喝到奶茶？

### 输入
The first line of input consists of a single integer T (1 ≤ T ≤ 25), denoting the number of test cases.

Each test case starts with a line of a single integer n (1 ≤ n ≤ 1e6), the number of classes. For the next n lines, each containing two integers a, b (0 ≤ a, b ≤ 1e9), denoting the number of students of the class and the number of cups of milk tea made by this class, respectively.

It is guaranteed that the sum of n over all test cases does not exceed 6×106.

**H_On の 贴心翻译：**第一行是一个单独的整数 T (1 ≤ T ≤ 25) 表示测试样例个数<br>
每个样例的第一行是一个整数 n (1 ≤ n ≤ 106) 表示有 n 个班<br>
接下来的 n 行每一行有两个整数 a, b (0 ≤ a, b ≤ 109) 表示这个班的 人数 和 奶茶数<br>
保证测试数据 n 的和不超过 6e6

### 输出
For each test case, print the answer as a single integer in one line.

**H_On の 贴心翻译：**对于每组测试样例，输出一个独占一行的整数作为答案

### 样例输入
```
1
2
3 4
2 1
```
__额外样例__
```
2
3 100 1 50 50 1 100
4 1 1 6 3 1 2 1 3
```
上面那一组样例是我做题的时候以为自己做出来了被别人提出的一组（我当时输出 150 正解是 151）<br>
下面那组是我搜题解的时候别人提出的，就顺手放上了

### 样例输出
```
3
```
__额外样例__
```
151
9
```

### 解题思路 - 伪
~~建议先跳过这一板块~~<br>
一开始我是考虑：（错误思路 - 1）<br>
把所有的人数和奶茶数存起来 (a[i], b[i]) 然后把奶茶总数算出来 (sb) ，然后遍历 1 - n 每次用总数减去 b[i] 表示这个班能喝的奶茶数，然后取 `min(a[i], sb - b[i])` 的值从没有喝的总奶茶数里减去，也就是 `sb -= min(a[i], sb - b[i])` 。<br>
但是这样有一个问题就是，如果判断的班级的奶茶已经被喝过了那么还会减去，这样的话这个班 的人可以喝的奶茶就变少了，所以答案当然是错的。

然后我这样考虑：（错误思路 - 2）<br>
遍历 1 - n 然后把这个班匹配到的奶茶数，也就是 `min(a[i], sb - b[i])` 从靠前的班开始依次减去（直接从 b[i] 里减，这样之后可以继续 sb - b[i]）。这样一来就能解决有可能奶茶喝到后面却重复判断的情况了。<br>
**但是！**只是单纯的从前到后是不行的！额外样例的第一个就是原因：<br>
1 班的 100 个人先喝 2 班的 50 杯再喝 3 班的 50 杯，是 100 杯<br>
然后 2 班的 50 人喝 1 班的 1 杯再喝 3 班的 49 杯，是 150 杯<br>
最后 3 班莫得喝了。。。但其实如果 2 班喝 3 班的 50 杯，1 班的那一杯就可以给 3 班的那一个人喝了

所以最后我决定：（。。。）<br>
先把 a[i], b[i] 从用值和下标的方式组对并小到大排序
 > 操作就是用 stl 的映照容器 map<int, int>
 >
 > 储存的是 amap(a[i], i) 和 bmap(b[i], i)

然后让人数少的班的人从杯数少的班的奶茶开始挨个喝，这里涉及的问题就是，更新<br>
因为喝完了要把那个班的奶茶数据从 bmap 中删除，更难受的是如果到最后一个班的没喝完，因为杯数是键（map 中的元素是 "<键, 值>对" 值可以变，但键不能）所以只能删除然后再插入一个新的键值对 ``bmap.erase(bp), bmap.insert(pair<int, int>(b[i] - left, i)`` 应该就是这个原因导致。。。**时！间！超！限！**结果是对的但是时间超限了，总之当时剩下的时间都在研究怎么节约时间了（反正也做不出来啥东西= =）

ps：这里写出我的思路只是希望能有人说不定会有别的启发，没有不要打我 wo<br>
上面那一堆里有一些变了样子的代码，没什么意义哈 =w= 想要我的~~错误~~代码的可以私信或者评论我（[的 CSDN 博客](https://blog.csdn.net/qq_37504214/article/details/99684137)），这里暂时不开放这些功能~~因为我不会搞呗~~

### 解题思路
总而言之比赛结束了也没研究出来怎么消减时间，在彻底绝望之后开始找题解，有人说是贪心，但是代码跟我的一样长<br>
后来又发现一个骨骼精奇的博客，就是这位：[点点点啊](https://blog.csdn.net/chimchim04/article/details/99589863#comments)<br>
看到了一个神奇的东西叫 Hall's marriage theorem 定理，博客里面还分享了一个讲解 Hall's marriage theorem 定理的链接，但是我看不懂然后我就自己想办法理解了这个**解题思路**。

总之原理就是找到**最大的能够配对的奶茶数**和**总人数**，然后取较小的值就是答案，之前是从匹配人在思考，这里是匹配奶茶！有点像是逆向思维？~~雾草草草真的思路清奇啊~~

#### 接下来我说一下具体操作
1. 先把这些数列出来方便看

|类别|a-人数|b-杯数|
|--|--|--|
|1|3|4|
|2|2|1|
|sum|sa = 5|sb = 5|
2. 然后来计算对于每一个数据的可匹配情况<br>
对于 1 班的奶茶来说，可以匹配的人数为 5 - 3 = 2 ，有 4 杯能匹配所以是 2 杯<br>
对于 2 班的奶茶来说，可以匹配的人数为 5 - 2 = 3 ，有 1 杯能匹配所以是 1 杯<br>
所以总共能匹配的奶茶数就是 3 ，而总人数是 5 ，因此很显然最后只有 3 个人能喝到奶茶<br>
其他样例可以自行尝试

#### 啊终于要上代码了
因为刚才解释思路的时候用的的变量名都一样，代码就不注释了
```
#include <iostream>

#define SNUM int(1e6 + 9)
#define ll long long

using namespace std;

ll a[SNUM];
ll b[SNUM];

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	int t;
	cin >> t;
	while (t--)
	{
		int n;
		cin >> n;
		ll sa = 0, sb = 0;
		for (int i = 0; i < n; i++) cin >> a[i] >> b[i], sa += a[i];
		for (int i = 0; i < n; i++) b[i] = min(b[i], sa - a[i]), sb += b[i];
		cout << min(sa, sb) << endl;
	}
	return 0;
}
```

其实难题一方面是算法，一方面是思路 ~ <br>
之前我们做总结时也有人说：其实算法题对于大佬来说就是所谓的“水题”，因为直接就知道怎么写，人家脑子里有模板直接就出来了所以对于他们来说很简单，难题主要还是需要大家开阔思路 ~ 我觉得挺有道理的吧
