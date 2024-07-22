---
title: POJ - 3268 Silver Cow Party
date: 2022-11-07 20:06:25
tags:
- 最短路
- kuangbing
categories:
- kuangbing
- 专题四 最短路练习
---

## [POJ - 3268 Silver Cow Party](https://www.acwing.com/problem/content/1134/)

N 头牛要去参加在某农场举行的一场编号为 X 的牛的派对。

有 M 条有向道路，每条路长 Ti；每头牛参加完派对后都必须回到家，每头牛都会选择最短路径。

求这 N 头牛的最短路径（一个来回）中最长的一条的长度。

特别提醒：可能有权值不同的重边。

输入格式
第一行包含三个整数 N,M,X。

接下来 M 行，每行包含三个整数 Ai,Bi,Ti，表示有一条从 Ai 到 Bi 的路，长度为 Ti。

输出格式
共一行，一个数，表示最短路中最长的一条的长度。

数据范围
1≤N≤1000,
1≤X≤N,
1≤M≤105,
1≤Ti≤100
输入样例：
```
4 8 2
1 2 4
1 3 2
1 4 7
2 1 1
2 3 5
3 1 2
3 4 4
4 2 3
```
输出样例：
```
10
```

## 题解
**我们找除所有最短路径的中的最大值**
这里的路径由两部分组成
1. 从我们的源点到派对地方
2. 从派对的地方回到起点

第一部分是一个多源点最短路问题，所以我们一开始反向建图跑单源最短路
第二部分是一个普通的单源最短路问题

## CODE
```C++
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
const int N = 1010, M = 100010;

int h1[N],h2[N],e[M],ne[M],w[M],idx;
int n,m,sta;
int dist1[N], dist2[N];

void add(int h[], int a, int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx ++;
}

void dijkstra(int h[], int dist[])
{
    bool st[N];
    memset(st, 0, sizeof st);
    memset(dist, 0x3f, sizeof dist1);
    priority_queue<PII,vector<PII>,greater<PII>> heap;
    heap.push({0,sta});
    dist[sta] = 0;

    while(heap.size())
    {
        auto t = heap.top();
        heap.pop();

        int ver = t.se, dis = t.fi;
        if(st[ver]) continue;
        st[ver] = true;

        for(int i = h[ver]; i != -1; i = ne[i])
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

int main()
{
    memset(h1,-1,sizeof h1);
    memset(h2,-1,sizeof h2);
    cin >> n >> m >> sta;

    for(int i = 0; i < m; i ++)
    {
        int a, b, c;
        scanf("%d%d%d",&a,&b,&c);
        add(h1,b,a,c);
        add(h2,a,b,c);
    }

    dijkstra(h1, dist1);
    dijkstra(h2,dist2);

    int res = -1;
    for(int i = 1; i <= n; i ++)
    {
        res = max(res, dist1[i] + dist2[i]);
    }
    cout << res << endl;
    return 0;
}
```