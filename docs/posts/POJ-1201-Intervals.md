---
title: POJ - 1201 Intervals
date: 2022-07-22 20:09:02
tags:
- 差分约束
- 图论
catergories:
- - 图论
- - 差分约束
---

## [POJ - 1201 Intervals](https://vjudge.net/problem/POJ-1201)

You are given n closed, integer intervals [ai, bi] and n integers c1, ..., cn.
Write a program that:
reads the number of intervals, their end points and integers c1, ..., cn from the standard input,
computes the minimal size of a set Z of integers which has at least ci common elements with interval [ai, bi], for each i=1,2,...,n,
writes the answer to the standard output.
Input
The first line of the input contains an integer n (1 <= n <= 50000) -- the number of intervals. The following n lines describe the intervals. The (i+1)-th line of the input contains three integers ai, bi and ci separated by single spaces and such that 0 <= ai <= bi <= 50000 and 1 <= ci <= bi - ai+1.
Output
The output contains exactly one integer equal to the minimal size of set Z sharing at least ci elements with interval [ai, bi], for each i=1,2,...,n.

Sample
Input
```
5
3 7 3
8 10 3
6 8 1
1 3 1
10 11 1
```
Output
```
6
```

### 题解
本题要我们去求的选取区间的中的元素个数最少。比如要求[1,6]至少选一个元素，[2,7]至少选一个元素，对于上面这个区间我们只需在其区间交集处选取元素，所以这个问题很有可能是一个`线段树问题 + 贪心`。
算法一`树状数组 + 贪心`
（待补）

算法二 `差分约束`
差分约束无非就是求解不等式系统问题，所以关键的是我们如果构造出不等式组。
在此借用前缀和数组的思想`s[i]`表示从1～i中选取`j`个元素
因为在这里我们需要求最小值，所以应该化成最长路问题
约束条件
1. `s[i] >= s[i-1]`
2. `s[i] - s[i-1] <= 1 ` -> `s[i-1] >= s[i] -1 1`(相隔一位的区间最多只能选一个或者不选)
3. `s[b] - s[a] >= c` -> `s[b] >= s[a] + c` （因为我们需要在这个区间中选取至少c个元素）
4. s[0]第0个元素之前没有元素可以选
**i的下标取值范围[1,50001]，差分约束这里的边要开到点数的三倍，有三个不等数组三组约束**
本题
构造完不等式组我们就可以建图(不会建图可以看这篇[差分约束](差分约束.md)，计算最长路即可。

### CODE
```C++
#include<bits/stdc++.h>
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
ll powmod(ll a,ll b) {ll res=1;a%=mod; assert(b>=0); for(;b;b>>=1){if(b&1)res=res*a%mod;a=a*a%mod;}return res;}
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head
const int N = 50010, M = N * 3;

int n;
int h[N],e[M],w[M],ne[M], idx;
bool st[N];
int dist[N],q[N];

void add(int a, int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx ++;
}

void spfa()
{
    memset(dist, -0x3f, sizeof dist);
    memset(st, 0, sizeof st);

    int hh = 0, tt = 0;
    q[tt ++] = 0;
    st[0] = true;
    dist[0] = 0;

    while(hh != tt)
    {
        int t = q[hh ++];
        if(hh == N) hh = 0;
        st[t] = false;

        for(int i = h[t]; ~i; i = ne[i])
        {
            int j = e[i];
            // 最长路
            if(dist[j] < dist[t] + w[i])
            {
                dist[j] = dist[t] + w[i];
                if(!st[j])
                {
                    q[tt ++] = j;
                    if(tt == N) tt = 0;
                    st[j] = true;
                }
            }
        }
    }
}


int main()
{
    cin >> n;

    // 前缀和数组从一开始（都加一）
    // 初始化 si >= si-1 si-1 >= si - 1
    memset(h,-1,sizeof h);
    for(int i = 1; i <= 50001; i ++)
    {
        add(i,i-1,-1);
        add(i-1, i, 0);
    }
    // s[b] >= s[a-1] + c
    for(int i = 0; i < n; i ++)
    {
        int a, b, c;
        scanf("%d%d%d",&a,&b,&c);
        a ++, b ++;
        add(a-1, b, c);
    }

    spfa();

    cout << dist[50001] << endl;
    return 0;
}
```