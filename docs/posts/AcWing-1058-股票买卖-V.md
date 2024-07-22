---
title: AcWing 1058. 股票买卖 V
date: 2022-09-17 17:06:39
tags:
- 动态规划
- 状态机
categories:
- AcWing
- 动态规划
- 状态机模型
---

## [AcWing 1058. 股票买卖 V](https://www.acwing.com/problem/content/1060/)
给定一个长度为 N 的数组，数组中的第 i 个数字表示一个给定股票在第 i 天的价格。

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。
输入格式
第一行包含整数 N，表示数组长度。

第二行包含 N 个不超过 10000 的正整数，表示完整的数组。

输出格式
输出一个整数，表示最大利润。

数据范围
1≤N≤105
输入样例：
```
5
1 2 3 0 2
```
输出样例：
```
3
```
样例解释
对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]，第一笔交易可得利润 2-1 = 1，第二笔交易可得利润 2-0 = 2，共得利润 1+2 = 3。

## 题解
设置一个dp[N][3]数组表示的意思 
 dp[i][0] 表示在i天手上有股票 dp[i][1] 表示在i天手上没有股票一天 dp[i][2] 表示在i天手上没有股票的第二天以后
状态转移模型
<img src ="/images /懒标记的解释.png">

根据状态机模型的转换图我们可以看出来状态转移图有环，且有可能存在负环。但是实际上是这样吗？？如果下一个状态会被上一个状态影响的话这样的是无法使用dp正常做法做的，当时要知道这个图很明确的表现出第i层状态是通过第i-1层状态转移过来的所以是可以使用dp求解而不需要使用图论的最短路算法求解.

### CODE
```C++
#include <bits/stdc++.h>
using namespace std;
#define rep(i,a,n) for (int i=a;i<n;i++)
#define per(i,a,n) for (int i=n-1;i>=a;i--)
#define pb push_back
#define eb emplace_back
#define mp make_pair
#define all(x) (x).begin(),(x).end()
#define fi first
#define se second
#define SZ(x) ((int)(x).size())
typedef vector<int> VI;
typedef basic_string<int> BI;
typedef long long ll;
typedef pair<int,int> PII;
typedef double db;
const ll mod=1000000007;
const int INF = 0x3f3f3f3f;
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head
const int N = 100010;

// 在i天dp[i][0] 表示的当前手上有股票 dp[i][1] 表示在i天手上没有股票一天 dp[i][2] 表示在i天手上没有股票的第二天以后
int dp[N][3]; 
int n;
int w[N];

int main()
{
    cin >> n;

    for(int i = 1; i <= n; i ++)
        cin >> w[i];

    dp[0][0] = dp[0][1] = -INF; // 表示初始状态不能是这个状态
    dp[0][2] = 0;

    for(int i = 1; i <= n; i ++)
    {
        dp[i][0] = max(dp[i-1][0], dp[i-1][2] - w[i]);
        dp[i][1] = dp[i-1][0] + w[i];
        dp[i][2] = max(dp[i-1][2], dp[i-1][1]);
    } 

    cout << max(dp[n][1], dp[n][2]) << endl;

    return 0;
}
```
