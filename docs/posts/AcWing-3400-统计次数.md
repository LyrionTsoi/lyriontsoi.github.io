---
title: AcWing 3400. 统计次数
date: 2022-12-29 11:10:09
tags:
- 枚举
- 清华大学机试题
categories:
- AcWing
- 考研机试题
- 枚举
---

# AcWing 3400. 统计次数（清华大学机试题）

给定两个正整数 n 和 k，求从 1 到 n 这 n 个正整数的十进制表示中 k 出现的次数。

输入格式
共一行，包含两个整数 n 和 k。

输出格式
输出一个整数，表示答案。

数据范围
1≤n≤106,
1≤k≤9
输入样例：
```
12 1
```
输出样例：
```
5
```
样例解释
从 1 到 12 这些整数中包含 1 的数字有 1,10,11,12，一共出现了 5 次 1。

# CODE

```c++
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
const int N = 1000010;

int n, k;

int main()
{
    cin >> n >> k;

    int res = 0;
    for(int i = 1; i <= n; i ++) 
    {
        int nu = i;
        while(nu != 0)
        {
            int t = nu % 10;
            if(t == k) res ++;
            nu /= 10;
        }
    }


    cout << res << endl;
    return 0;
}
```

