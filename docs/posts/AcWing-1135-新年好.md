---
title: AcWing 1135.新年好
date: 2022-07-19 15:58:56
tags:
- 单源最短路
- 枚举
categories:
- - 图论
- - 最短路
---

## [AcWing 1135.新年好](https://www.acwing.com/problem/content/1137/)
重庆城里有 n 个车站，m 条 双向 公路连接其中的某些车站。
每两个车站最多用一条公路连接，从任何一个车站出发都可以经过一条或者多条公路到达其他车站，但不同的路径需要花费的时间可能不同。
在一条路径上花费的时间等于路径上所有公路需要的时间之和。
佳佳的家在车站 1，他有五个亲戚，分别住在车站 a,b,c,d,e。
过年了，他需要从自己的家出发，拜访每个亲戚（顺序任意），给他们送去节日的祝福。
怎样走，才需要最少的时间？

输入格式
第一行：包含两个整数 n,m，分别表示车站数目和公路数目。
第二行：包含五个整数 a,b,c,d,e，分别表示五个亲戚所在车站编号。
以下 m 行，每行三个整数 x,y,t，表示公路连接的两个车站编号和时间。

输出格式
输出仅一行，包含一个整数 T，表示最少的总时间。

数据范围
1≤n≤50000,
1≤m≤105,
1<a,b,c,d,e≤n,
1≤x,y≤n,
1≤t≤100
输入样例：
```
6 6
2 3 4 5 6
1 2 8
2 3 3
3 4 4
4 5 5
5 6 2
1 6 7
```
输出样例：
```
21
```

### 题解
由题目知道需要从源点到5个点经过路径的最短
一. 枚举拜访顺序，对每一种拜访顺序做最短路
假如我们的摆放顺序为 `a b c d e`,那么我们需要做五次最短路 1->a的最短路 2->b的最短路....
这中枚举方案的时间复杂度O（5！ * n^2)  
二. 先做预处理，再枚举摆放顺序
因为很清楚知道其实不用每次都计算最短路。因为路都是固定的，变的是走的策略，所以可以一开始就预处理最短路，在枚举。

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
ll powmod(ll a,ll b) {ll res=1;a%=mod; assert(b>=0); for(;b;b>>=1){if(b&1)res=res*a%mod;a=a*a%mod;}return res;}
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head

const int N = 50010, M = 200010;

int n, m, ans = INF;
int h[N], e[M], ne[M],w[M], idx;
int dist[6][N];
bool st[N];
int sourse[6] = {1};

void add(int a, int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx++;
}

void dijkstra(int start, int dist[])
{
    memset(dist, 0x3f, N * 4);
    memset(st, 0, sizeof st);

    dist[start] = 0;
    priority_queue<PII,vector<PII>,greater<PII>> heap;
    heap.push({0,start});

    while(heap.size())
    {
        auto t = heap.top();
        heap.pop();

        int ver = t.second;
        if(st[ver]) continue;
        for(int i = h[ver]; ~i; i = ne[i])
        {
            int j = e[i];
            if(dist[j] > dist[ver] + w[i])
            {
                dist[j] = dist[ver] + w[i];
                heap.push({dist[j],j});
            }
        }
    }
}

void dfs(int u, int start, int distance)
{
    if(u > 5) 
        ans = min(ans,distance);

    for(int i = 1; i <= 5; i ++)
    {
        if(!st[i])
        {
            int next = sourse[i];
            st[i] = true;
            dfs(u + 1, i, distance + dist[start][next]);
            st[i] = false;
        }
    }
}

int main()
{
    // 本题由于所有边是正权边，且nm大概是5e9的级别的所以spfa是不敢使用的
    // 堆优化的dijkstrea是mlogn大概在1e6的级别，朴素版的在2.5e9所以应该使用的是堆优化
    cin >> n >> m;
    for(int i = 1; i < 6; i ++) 
        scanf("%d",&sourse[i]);
    memset(h,-1,sizeof h);
    for(int i = 0; i < m; i ++)
    {
        int a,b,c;
        cin >> a >> b >> c;
        add(a,b,c),add(b,a,c);
    }

    for(int i = 0; i < 6; i ++)
        dijkstra(sourse[i], dist[i]);

    memset(st,0,sizeof st);
    dfs(1,0,0);

    cout << ans << endl;
    return 0;
}
```