---
title: LeetCode 1081. Smallest Subsequence of Distinct Characters
date: 2022-07-16 10:44:19
tags:
- 单调栈
- 贪心
categories: 
- - 数据结构
---


## [LeetCode 1081. Smallest Subsequence of Distinct Characters](https://leetcode.com/problems/smallest-subsequence-of-distinct-characters/)

题目描述
返回字符串 text 中按字典序排列最小的子序列，该子序列包含 text 中所有不同字符一次。

样例
输入：`"cdadabcc"`
输出：`"adbc"`
输入：`"abcd"`
输出：`"abcd"`
输入：`"ecbacba"`
输出：`"eacb"`
输入：`"leetcode"`
输出：`"letcod"`
注意
1 <= text.length <= 1000
text 由小写英文字母组成。

### 题解
单调栈  + 贪心
要想字典序越小那么我们的第一个字符一定是越小越好，于是我们就需要维护的是一个单调递增的单调栈
入栈： 
 1. （如果这个字符后面还会再出现） 要求这个字符不在栈中并且栈顶元素比他要更大
 2. (如果这个字符在后面不会再出现）这个字符不在栈中（由于题目要求最后求的字符串每个字符都要出现）所以这个字符一定要入栈

出栈： 
 1. 如果栈顶这个字符后面还会再出现，并且当前栈顶字符大于当前字符的话，那么栈顶字符出栈
 2. 反之栈顶在这个字符不会再出现，就不能出栈

在这里我们用cnt来保存每个字符出现次数，然后再遍历原字符串的时候，每次字符出现cnt--，这里的cnt数组记录的是从这个字符往后的还会出现多少次，如果为0了那么往后就不会再出现了。

### CODE
```c
#include <vector>
#include <cstring>
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <stack>
#include <queue>
#include <deque>
#include <set>
#include <map>
#include<cmath>

#define LL long long
#define pii pair<int,int>


using namespace std;

const int INF = 0x3f3f3f3f,N = 100010;

int cnt[30];    // 记录每个字符出现的次数
char st[N];  //   单调递增栈
bool sta[30]; // 记录字符是否在栈里面出现了
string s;

int main()
{
    cin >> s;
    s = " " + s;
    int n = s.size();
    // 记录每个字符出现的次数
    for(int i = 1; i < n; i ++)
        cnt[s[i]-'a'] ++;


    // 单调栈 + 贪心 处理 字典序问题
    int hh = 0; st[0] = ' ';
    memset(st,false, sizeof st);
    for(int i = 1; i < n; i ++)
    {
        // > 表示了单调递增，cnt[] > 0 说明了后面这个元素至少还会出现一次
        cnt[s[i] - 'a'] --; //  每次字符出现一次就把他减去，表示的是从这个字符位置往后还有多少个
        if(sta[s[i] - 'a']) continue;
        // 出栈，更新栈顶指针与栈顶元素的状态
        while(hh && st[hh] > s[i] && cnt[st[hh] - 'a'] > 0) sta[st[hh] - 'a'] = false, hh --;// 这里要注意hh的顺序是在后面的
        st[++hh] = s[i];
        sta[s[i] - 'a'] = true;
    }

    for(int i = 1; i <= hh; i ++) cout << st[i];
    puts("");

    return 0;
}
```
