---
layout: post
title: 火车发车问题 - stack 出入栈问题
date: 2019-05-21
description: 有关栈的使用的例题
tag: 题解
---

[题目链接 - hdu1022](http://acm.hdu.edu.cn/showproblem.php?pid=1022)

### 题目
As the new term comes, the Ignatius Train Station is very busy nowadays. A lot of student want to get back to school by train(because the trains in the Ignatius Train Station is the fastest all over the world ^v^). But here comes a problem, there is only one railway where all the trains stop. So all the trains come in from one side and get out from the other side. For this problem, if train A gets into the railway first, and then train B gets into the railway before train A leaves, train A can't leave until train B leaves. The pictures below figure out the problem. Now the problem for you is, there are at most 9 trains in the station, all the trains has an ID(numbered from 1 to n), the trains get into the railway in an order O1, your task is to determine whether the trains can get out in an order O2.
![火车行动演示](/image/20190521/trainshow.png)
**H_On の 贴心翻译：**新学期到辣，伊格纳丢火车站最近会很忙。许多学生想坐火车回学校（因为德国医学世界第一！【不是】伊格纳丢火车站世界第一！），然后问题出现了，如果 A 车先进站，然后 B 车在 A 车出站之前也进站了，A 车就只能等 B 车出站以后才能离开了。下面的图片大概演示了一下这个尴尬的情况。然后日常推锅给你（工具人Acmer），现在最多有 9 趟火车会进站，所有的火车都有自己的 ID（数字 1 到 n ），火车进站次序是 O1 ，你的任务（功能）是判断按照出站次序 O2 能不能将火车全部发出。

### 输入
The input contains several test cases. Each test case consists of an integer, the number of trains, and two strings, the order of the trains come in:O1, and the order of the trains leave:O2. The input is terminated by the end of file. More details in the Sample Input.

**H_On の 贴心翻译：**多组输入样例，每组样例先是一个整数，表示火车数量，然后是两个字符串，表示火车进站次序：O1 ，和火车出站次序：O2 。输入以文件结束为标志（贴心tip：EOF）。更多详情请看样例输入。

### 输出
The output contains a string "No." if you can't exchange O2 to O1, or you should output a line contains "Yes.", and then output your way in exchanging the order(you should output "in" for a train getting into the railway, and "out" for a train getting out of the railway). Print a line contains "FINISH" after each test case. More details in the Sample Output.

**H_On の 贴心翻译：**如果不能把进站顺序 O1 变成出站顺序 O2 ，输出字符串 "No." 。如果可以就先输出一个 "Yes." ，然后输出进站出站的情况（火车进站就输出一个 "in" ，火车出站就输出一个 "out" ）。在每组样例的最后打印一个 "FINISH" 。更多详情请看样例输出。

### 样例输入
```
3 123 321
3 123 312
```

### 样例输出
```
Yes.
in
in
in
out
out
out
FINISH
No.
FINISH
```

### 解题思路
1. 使用 stack 栈来储存对内车次；
2. 用两个栈来存放进站车次和出站车次的顺序；
3. 将火车依次进站，直到站顶车次和第一个出站车次相同，将出站车次栈和站内车次栈栈顶出栈；
4. 继续判断下一个出站车次，执行第 3 步；
5. 若进站栈已空且出站栈也空说明能够依次出栈输出 Yes. 和出站顺序；若出站栈未空说明不能依次出站输出 No ；
6. 每组样例最后输出一个 HINISH 。

### 代码
注视很清晰哈
```
#include <iostream>
#include <stack>
#include <string>

#define S 1<<20

using namespace std;

int main()
{
	int tn;
	while (cin >> tn)
	{
		/** 判断出入站车次的标记  **/
		int p1, p2;

		/** 存储站内信息 **/
		stack<char> s;

		/** 出入站情况	- se **
		 ** 入站顺序	- o1 **
		 ** 出站顺序	- o2 **/
		string se, o1, o2;

		cin >> o1 >> o2;
		p1 = p2 = 0;

		/** 当出站车次没判断完的时候一直循环 **/
		while (p2<o2.length())
		{
			/** 如果车站里没车 **
			 ** 或者车站最前面的车次不是下一趟要发的车次 **
			 ** 就一直按照入站顺序将火车进站 **/
			if (s.empty() || s.top()!=o2[p2])
			{
				/** 判断下一趟要发的火车是否已经入站 **/
				int f = 0;
				/** 将车一直入站并添加记录 **
				 ** 直到下一趟要发的火车入站 **/
				while (p1<o1.length())
				{
					s.push(o1[p1++]);
					se.append("I");
					if (s.top()==o2[p2])
					{
						f = 1;
						break;
					}
				}
				/** 若匹配成功，发车并记录 **
				 ** 若匹配不成功，记录发车顺序无效 **/
				if (f) se.append("O"), s.pop();
				else if (p1==o1.length()) se.append("N");
			}
			/** 如果匹配上了，直接出站 **/
			else s.pop(), se.append("O");
			p2++;/** 依次判断下一位出站车次 **/
		}

		/** 如果有发车顺序无效的记录，直接输出 "No." **/
		if (se.find("N")!=-1) cout << "No." << endl;
		/** 反之输出 "Yes." 并按照记录输出出入站情况 **/
		else
		{
			cout << "Yes." << endl;
			for (int i=0; i<se.length(); i++)
				if (se[i]=='I') cout << "in" << endl;
				else if (se[i]=='O') cout << "out" << endl;
		}
		cout << "FINISH" << endl;/** 每组样例最后输出一个 "FINISH" **/
	}
	return 0;
}
```

### 说一下
一定要认真读题啊，这里就是想分享一下本人卡题的情况～
1. 以为是：
    * 匹配正确输出 yes 和顺序；
    * 匹配不正确的话就，依次判断，匹配上一个输出一个 FINISH；匹配不上就输出 No 。
2. 其实是：
    * 匹配上了输出 yes 和顺序，最后输出 FINISH；
    * 匹配不上输出 no 和 FINISH 。

总之就是卡了两天，最后去网上找题解，发现原来是这个问题。

不过现在的这个代码就是看了别人的题解优化过的，以下附赠原思路代码，仅供参考，可以不看=w=

```
//原先解法
#include <iostream>
#include <cstdio>
#include <stack>
#include <string>
#include <algorithm>

#define S 1<<20

using namespace std;

int main()
{
	int tn;
	while (cin >> tn)
	{
		stack<char> ig, og, s;
		string se;
		string t;
		cin >> t;
		for (int i=t.length()-1; i>=0; i--) ig.push(t[i]);
		cin >> t;
		for (int i=t.length()-1; i>=0; i--) og.push(t[i]);
		while (!og.empty())
		{
			if (s.empty())
			{
				int f = 0;
				while (!ig.empty())
				{
					s.push(ig.top());
					ig.pop();
					se += 'I';
					if (s.top()==og.top())
					{
						f = 1;
						break;
					}
				}
				if (f) se += 'O', s.pop();
				else if (ig.empty()) se.append("N");
			}
			else
			{
				if (s.top()==og.top()) s.pop(), se += 'O';
				else if (!ig.empty())
				{
					int f = 0;
					while (!ig.empty())
					{
						s.push(ig.top()), ig.pop(), se += 'I';
						if (s.top()==og.top())
						{
							f = 1;
							break;
						}
					}
					if (f) se += 'O', s.pop();
					else if (ig.empty()) se.append("N");
				}
				else se.append("N");
			}
			og.pop();
		}
		if (se.find("N")==-1)
		{
			cout << "Yes." << endl;
			for (int i=0; i<se.length(); i++)
			{
				switch (se[i])
				{
					case 'I':cout << "in" << endl;break;
					case 'O':cout << "out" << endl;break;
				}
			}
			cout << "FINISH" << endl;
		}
		else
		{
			cout << "No." << endl;
			cout << "FINISH" << endl;
		}
	}
	return 0;
}

```
