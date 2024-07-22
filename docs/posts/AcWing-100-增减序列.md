---
title: AcWing 100. 增减序列
date: 2023-02-26 22:02:04
tags:
- 差分
- AcWing
Categories:
- AcWing
- 蓝桥杯
- 差分
---

# [题意](https://www.acwing.com/problem/content/102/)

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202302262221706.png)

# 题解

一半对于这种一维空间的操作，都是考虑差分操作的。

这里的差分数组$b_i = a_i - a_{i-1}$,要想所有的数相同就只有 $[b_2, b_{n}]$全为0.

这里的所有操作有一下几种![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202302262224265.png)

- 第一种操作的意思为因为数组b可能存在正数和负数，那么这里的操作就是将正数-1，负数+1的操作，理论上是最优的操作。操作对象是 $[b_2,b_{n}]$
- 第二个操作是，数组只有一种数了（正数/负数），这时候只能对b1进行加1/减1操作，对$[b_2,b_{n}]$减一/加1操作
- 同理第三种操作，就是对n+1操作而已，我们的目的只是为了 $[b_2,b_{n}]$均为0
- 第四个操作没有任何意义，从差分数组角度上来看，我们目的是操作 $[b_2,b_n]$数组，所以对 $b_1 or b_{n+1}$操作并没有意义，从原数组角度上来看，对整个区间一块操作数字之间的相对距离是不会变的，所以没有任何意义。

那么我们需要操作的最少次数 = $min(p,q) + |p-q|= max{p,q}$，这里的p和q分别表示的是p正数之和，q表示负数之和。

具体的解释为我们应该选择最优的方案1，也就是正数-1，负数+1，这样只最少的操作就为 $min(p,q)$,那么最后只会剩下一种类型的数字 $|p-q|$,于是就是我们的最少方案

结果个数 =  $abs(p-q)+1$

因为数列的特性我们可以之后最后数列数值等于第一个数，当我们操作完操作一的时候，我们就要进行`2`和`3`操作。那么就可以操作0个操作2，$abs(p-q)$个操作3，1个操作2，$abs(p-q) - 1$个操作3。。。。这样的组合形式就有 $p-q+1$个，所以最后结果为 $abs(p-q)+1$

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

int a[N],b[N];
int n;

int main()
{
    cin >> n;
    for(int i = 1; i <= n; i ++) scanf("%d",&a[i]);
    
    for(int i = 1; i <= n; i ++) b[i] = a[i] - a[i-1];

    ll p = 0,q = 0;
    for(int i = 2; i <= n; i ++)
        if(b[i] > 0) p += b[i];
        else q -=b[i];

    cout << max(p,q) << endl;
    cout << abs(p-q) + 1 << endl;

    return 0;
}
```

