---
layout: post
title: 【Manacher】【马拉车】最长回文子串问题
date: 2022-02-28
author: H_On
description: 听说过，今天才专门搞懂
tag: 算法
---

题目链接：[LintCode-200-最长回文子串](https://www.lintcode.com/problem/200/?_from=collection&fromId=161)

- [问题大意](#问题大意)
- [马拉车算法逻辑](#马拉车算法逻辑)
- [代码和详细注释](#代码和详细注释)

### 问题大意
给你一个字符串，让你找出其中 `最长的` `连续的` `回文子串`
<br>如:
* `abaa -> aba`
* `abcdzdcab -> cdzdc`
* `fabccbab -> abccba`

### 马拉车算法逻辑
暴力当然可行，也经常超时，马拉车算法我也是刚刚了解，这里记录一个感觉网上教程大都缺少了介绍的一个点

1. 根据算法说明，先向所有字符中间插入一个特殊符号如 `#` 此目的是使原回文串无论是 **奇数串** 还是 **偶数串**，都可以变成以一个字符为中心的 **奇数串**，前后端还可以插入不同的特殊字符来使程序在循环遍历时自然无法越界
   <br>例如：
   * `abaa -> !#a#b#a#a#@ -> #a#b#a#` 结果是奇数串，插入字符后找到的是奇数串，中心字符是 `b`
   * `abba -> !#a#b#b#a#@ -> #a#b#b#a#` 结果是偶数串，插入字符后找到的还是奇数串，中心字符是 `#`
2. 寻找最大回文字串，这个过程比较复杂，接下来慢慢讲解：
   * 首先插入介绍一下马拉车的算法思想
        > 如上所述字符串已经处理过了<br>
        > 之后我们从左往右依次遍历每个字符 s[i]，并以 i 为中心向两边判断<br>
        > 直到遇到不相同的就停止，此时循环的次数即为 `以 i 为中心的回文串半径 r`<br>
   * 但很显然我们不能挨个这样做，不然时间复杂度又变成 n^2 了，所以要减少一些重复的判断
     1. 首先要记录 4 个变量和 1 个数组：
        |变量名|作用|修改|
        |-|-|-|
        |`r_range[dealed_s.length()]`|记录 `处理过的字符串` 的每个点的回文半径，容量是 `处理过的字符串` 的长度|初始化时分成两种情况：（阅读半径初始化的时候，里面的变量含义不清晰的可以先往下看看）<br>1. 如果当前点在 **最右回文串** 范围之内，则此点的半径可以初始化成<br>`min(r_range(rightmost_heart - (i - rightmost_heart)), rightmost_boundary - i)`<br>因为这个点 `rightmost_heart - (i - rightmost_heart)` 为 `i` 关于 `rightmost_heart` 这个点左边的对称点，因为 i 左边的点一定都是获取过回文半径的，因此在 `rightmost_heart` 这个点为回文中心的字符串范围内，对称点的回文情况一定是相同的，但是也有可能对称点的回文半径超过了 `rightmost_heart` 的范围，因此还要跟 `rightmost_boundary - i` 取一个最小值<br>2. 如果当前点在 **最右回文串** 范围之外，则附近的情况都是位置的，因此只能初始化为 `1`|
        |`rightmost_heart`|因为是从左往右遍历，因此左边的点都是已经求出回文半径的状态，所以在 **优化** 时只需要记录 **范围最靠右** 的回文串的信息即可|当当前点 `i` 为中心的回文串范围最右端 `i + r_range[i]` 超过已记录的最右端 `rightmost_boundary` 时，更新 最右中心点 `rightmost_heart = i`|
        |`rightmost_boundary`|记录 **范围最靠右** 的回文串 **最右端** 的下标|与 `rightmost_heart` 同步更新，条件一样，更新为 `rightmost_boundary = i + r_range[i]`|
        |`maximal_heart`|为结果做事，记录最长回文串的中心点|当 当前点 `i` 为中心的回文串半径已经大于 `maximal_range` 时，更新当前回文串为最大回文串 `maximal_heart = i`|
        |`maximal_range`|为结果做事，记录最长回文串的半径|与 `maximal_heart` 同步更新，此变量作为最大值的判断条件<br>`if (maximal_range < r_range[i]) {maximal_range = r_range[i]; maximal_heart = i;}`|

### 代码和详细注释
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
#include <vector>

int main() {
        ios::sync_with_stdio(0); cin.tie(0);
        string s; cin >> s;
        // step 1:
        // 将字符中间插入特殊字符，这是为了使最大回文字串无论是【奇】还是【偶】都可以同样判断成奇数串
        // 最前面前和最后面使用插入不同的字符来阻止越界
        string deal_s = "";
        deal_s += "!#";
        for (char c : s) deal_s += c, deal_s += "#";
        deal_s += "@";
        cout << deal_s << endl;
        // step 2:
        // 初始化判断最大回文数要使用的变量
        hip rightmost_heart = 0;        // 已经判断过的，范围最靠右的，回文串中心
        hip rightmost_boundary = 0;     // 已经判断过的，范围最靠右的，回文串右端点
        hip maximal_heart = 0;          // 回文半径最大的回文串中心
        hip maximal_range = 0;          // 回文半径最大的回文串半径
        // step 3:
        // 遍历处理过的字符串，寻找最大回文字串
        vector<hip> r_range(deal_s.size());
        for (hip i = 1; i < deal_s.size() - 1; i++) {
                // 首先确定当前半径
                // 1. 如果当前点在之前回文中心之内，则半径可能是【当前点到最大范围之间的距离（设为 i_to_rb = rightmost_boundary - i）】
                // 或是（i-i_to_rb~i+i_to_rb 可能不回文）i 关于 rightmost_heart 的对称点 other_i = rightmost_heart - (i - rightmost_heart) 的回文半径
                // 2. 如果 i 不在判断过的最右范围之中，则初始半径为 1
                // 【这里吐槽一下好多博客都给出了这个公式 2*rightmost_heart - i 却不解释，很难理解，其实就是找对称点: rightmost_heart - (i - rightmost_heart)
                r_range[i] = (i < rightmost_boundary) ? (min(rightmost_boundary - i, r_range[2*rightmost_heart - i])) : (1);
                // 遍历字符串找到此点最大的回文半径
                while (deal_s[i - r_range[i]] == deal_s[i + r_range[i]]) r_range[i] += 1;
                // 如果最右变的范围变了，就更新
                if (rightmost_boundary < i + r_range[i]) rightmost_boundary = i + r_range[i], rightmost_heart = i;
                // 如果最大半径变了，也更新
                if (maximal_range < r_range[i]) maximal_range = r_range[i], maximal_heart = i;
        }
        cout << "maximal_heart = " << maximal_heart << endl << "maximal_range = " << maximal_range << endl;
        // step 4:
        // 切片字符串并且输出结果
        for (hip i = maximal_heart - maximal_range + 1; i <= maximal_heart + maximal_range - 1; i++) {
                if (deal_s[i] == '!') continue;
                if (deal_s[i] == '@') continue;
                if (deal_s[i] == '#') continue;
                cout << deal_s[i];
        } cout << endl;
        return 0;
}
```