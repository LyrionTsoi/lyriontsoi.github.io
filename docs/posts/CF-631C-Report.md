---
title: CodeForces - 631C REPORT
date: 2022-07-12 20:35:40
tags: 
- 单调栈
categories: 
- - 数据结构
- CodeForces
---

## [CF 631C REPORT](https://codeforces.com/contest/631/problem/C)

Each month Blake gets the report containing main economic indicators of the company "Blake Technologies". There are n commodities produced by the company. For each of them there is exactly one integer in the final report, that denotes corresponding revenue. Before the report gets to Blake, it passes through the hands of m managers. Each of them may reorder the elements in some order. Namely, the i-th manager either sorts first ri numbers in non-descending or non-ascending order and then passes the report to the manager i + 1, or directly to Blake (if this manager has number i = m).

Employees of the "Blake Technologies" are preparing the report right now. You know the initial sequence ai of length n and the description of each manager, that is value ri and his favourite order. You are asked to speed up the process and determine how the final report will look like.

Input
The first line of the input contains two integers n and m (1 ≤ n, m ≤ 200 000) — the number of commodities in the report and the number of managers, respectively.

The second line contains n integers ai (|ai| ≤ 109) — the initial report before it gets to the first manager.

Then follow m lines with the descriptions of the operations managers are going to perform. The i-th of these lines contains two integers ti and ri (, 1 ≤ ri ≤ n), meaning that the i-th manager sorts the first ri numbers either in the non-descending (if ti = 1) or non-ascending (if ti = 2) order.

Output
Print n integers — the final report, which will be passed to Blake by manager number m.

### 思路
在本题一共只有两种操作op1是非严格递减op2是非严格递增（也就是说里面可能有相同的数字）
**优化一**  对于不同的操作，只要后一个操作操作的区间比上一个区间要更加的大的话那么上一个区间的操作就无效，因为会被覆盖掉。所以我们对操作区间做一个单调递减的记录（使用单调栈）
**优化二**  经过第一步的优化我们可以的得到一个单调递减的操作区间。 对于[1,5],[1,3]区间操作op1，op2。在这个尝试探讨op看是否有特殊性质
op1 == op2（操作相同）
op2没有意义必然被大区间的操作覆盖
op1 != op2
假设大区间是非严格递减小区间是非严格递增，那么[4,5]这个区间是[1,5]这个区间是整个大区间里最小的两个数
假设大区间是非严格递增小区间是非严格递减，那么[4,5]这个区间是整个大区间里的最大的两个数

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
#define fi first
#define se second


using namespace std;

const int INF = 0x3f3f3f3f, N = 200010;

int st[N][2];
int n, m;
int arr[N];
int ans[N];

int main()
{
    // 2是降序 1是升序
    cin >> n >> m;
    for(int i = 0; i < n; i ++) scanf("%d",&arr[i]);

    int hh = 0;st[0][0] = 0,st[0][1] = 1;
    // st[1]是栈底 st[1][0]为栈底第一元素，表示的是操作范围,st[1][1]为栈底第二元素表示是操作类型
    for(int i = 0; i < m; i ++)
    {
        int a, op;cin >> op >> a;
        while(hh && st[hh][0] <= a) hh --;
        st[++hh][0] = a, st[hh][1] = op;
    }

    sort(arr,arr + st[1][0]);

    int l = 0, r = st[1][0], k = hh + 1;
    int p = r;
    st[k][0] = 0,st[k][1] = 1;   // 处理边界
    for(int i = 1; i < k; i ++)
    {
        int op = st[i][1];
        while(p > st[i+1][0])
        {
            p --;
            // 升序取最大值 降序取最小值（这里使用双指针对其赋值）
            if(op == 1) ans[p] = arr[--r];
            else ans[p] = arr[l++];
        }
    }
    for(int i = st[1][0]; i < n; i ++) ans[i] = arr[i];

    for(int i = 0; i < n; i ++) cout << ans[i] << ' ';

    return 0;
}
```