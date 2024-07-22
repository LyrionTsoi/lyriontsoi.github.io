---
title: 移掉K位数字
date: 2022-07-15 22:22:44
tags:
- 单调栈
- 贪心
categories: 
- 单调栈
---

## [移掉K位数字](https://www.acwing.com/problem/content/1455/)

给定一个以字符串表示的非负整数 num，移除这个数中的 k 位数字，使得剩下的数字最小。

注意：

空字符串被视为0。
如果结果中包含前导零，则需要将前导零删除，最后删除的前导零不用包含在移除的 k 个数字中。
输入格式
第一行输入一个字符串，用来表示非负整数 num。

第二行输入一个整数，表示 k。

输出格式
输出一个字符串，表示移除 k 位数字后所能得到的最小数字。

数据范围
0≤k≤ 字符串长度 ≤100000，
num 中不包含任何前导 0。

输入样例1：
```
1432219
3
```
输出样例1：
```
1219
```
样例1解释
移除掉三个数字 4,3,2 可形成一个新的最小的数字 1219。

输入样例2：
```
10200
1
```
输出样例2：
```
200
```
样例2解释：
移掉首位的 1 剩下的数字为 200. 注意输出不能有任何前导零。

输入样例3：
```
10
2
```
输出样例3：
```
0
```
样例3解释
从原数字移除所有的数字，剩余为空就是 0。

## 题解
因为输出数的数的为数是确定的 == n-k 位，所以最高位越小对于整个的数值就越小，这里可以结合带权展开
所以我们需要使用一个单调栈来构造一个递增序列
还要注意的一些细节
全部删除输出 0
单调栈里面全部为0是保留一位0
如果这个数不是全为0，前面的0就是前导0，所以要全部去掉，这里可以使用i < n来判断

## CODE
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

const int INF = 0x3f3f3f3f, N = 200010;

char st[N];
char s[N];
int n,k;

int main()
{
    cin >> n;
    for(int i = 1; i <=n ;i ++) cin >> s[i];
    cin >> k;
    int hh = 0; st[hh] = '0';
    int ck = k;
    for(int i = 1; i <= n; i ++)
    {
        while(hh && st[hh] > s[i] && ck)
        {
            hh--,ck --;
        }
        st[++hh] = s[i];
    }

    // 没有删完的情况ck>0,因为没有删完的元素全部入栈了，所以我们需要从栈顶开始退栈
    while(ck --) hh--;

    if(n-k == 0) cout << "0" << endl;
    else
    {
        int i = 1;
        while(st[i] == '0' && i < hh) i ++;  // 删去前导0，全是0的情况但是至少要保留一个0
        for(int j = i; j <= hh; j ++) cout << st[j];
    }


    return 0;
}
```
