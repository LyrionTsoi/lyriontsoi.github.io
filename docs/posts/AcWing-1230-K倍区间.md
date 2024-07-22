---
title: AcWing 1230. K倍区间
date: 2023-02-21 21:53:02
tags:
- Acwing
- 前缀和
Categories:
- Acwing
- 蓝桥杯
- 前缀和
---

# 题目

给定一个长度为 N 的数列，A1,A2,…AN，如果其中一段连续的子序列 Ai,Ai+1,…Aj 之和是 K 的倍数，我们就称这个区间 [i,j] 是 K 倍区间。 

你能求出数列中总共有多少个 K 倍区间吗？ 

**输入格式**

第一行包含两个整数 N 和 K。

以下 N 行每行包含一个整数 Ai。

**输出格式**

输出一个整数，代表 K 倍区间的数目。

**数据范围**

1≤N,K≤100000,
1≤Ai≤100000

**输入样例：**

```
5 2
1
2
3
4
5
```

**输出样例：**

```
6
```

# 题解

通过前缀和数组我们可以知道区间$[l,r]$之和 =  $sum[r] - sum[l-1]$,要想区间 $[l,r]$是k的倍数，那么就应该符合 $(sum[r] - sum[l-1])%k=0$
$$
\begin{split}
(sum[r] - sum[l-1])\  mod \ k = 0;\\
sum[r]\ mod\ k = sum[l-1]\ mod\ k; \\
\end{split}
$$
可以从是上面知道只要区间两端与k取模，余数相同的话，那么这个区间之和就一定是k的倍数。

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
const int N = 100010;
ll s[N];
int cnt[N];
int n, K;

int main()
{
    cin >> n >> K;
    for(int i = 1; i <= n; i ++)
    {
        scanf("%d",&s[i]);
        s[i] += s[i-1];
    }

    // 枚举右端点
    ll ans = 0;
    cnt[0] = 1; // 一个都不选的时候 0%k=0
    for(int r = 1; r <= n; r ++)
    {
        ans += cnt[s[r] % K];
        cnt[s[r] % K] ++;
    }

    cout << ans << endl;

    return 0;
}
```

