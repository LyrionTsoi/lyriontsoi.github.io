---
title: AcWing 4241. 货物运输
date: 2022-11-05 15:05:05
tags:
- 最小生成树
- 最短路
categories:
- Kuangbing
- 专题4 最短路练习
---

# [AcWing 4241. 货物运输](https://vjudge.net/problem/POJ-1797)
背景
Hugo Heavy 很高兴。在 Cargolifter 项目崩溃后，他现在可以扩展业务。但是他需要一个聪明的人来告诉他是否真的有一条路可以从他的客户建造他的巨型钢起重机的地方到所有街道都可以承受重量的地方。
幸运的是，他已经制定了城市规划，包括所有街道和桥梁以及所有允许的重量。不幸的是，他不知道如何找到最大承重能力，以便告诉客户起重机的重量。但你肯定知道。

问题
你会得到城市的平面图，由交叉口之间的街道（有重量限制）描述，从 1 到 n 编号。你的任务是找出从 1 号路口（雨果所在的地方）到 n 号路口（客户所在的地方）可以运输的最大重量。您可以假设至少有一条路径。所有街道都可以双向行驶。

输入
第一行包含场景的数量（城市规划）。对于每个城市，街道交叉口的数量 n (1 <= n <= 1000) 和街道数量 m 在第一行给出。以下 m 行包含整数的三倍，指定街道的起点和终点交叉口以及最大允许权重，为正且不大于 1000000。每对交叉口之间最多有一条街道。

输出
每个场景的输出都以包含“场景 #i:”的行开头，其中 i 是场景的编号，从 1 开始。然后打印一行，其中包含 Hugo 可以运输给客户的最大允许重量。用空行终止场景的输出。

Sample
**INPUT**
```
1
3 3
1 2 3
1 3 4
2 3 5
```

**OUTPUT**
```
Scenario #1:
4

```

## 题解
方法一 最短路
改写dijkstra的dist数组，dist的属性从到最源点的最短距离改成**代表1到i点的所有边中最小值最大的一个**

然后再用堆去求解即可

但是这个方法有一个问题是我一直没有解决的，堆优化的dijkstra算法是贪心做法，本题的改变dist属性的过程中不太了解本属性为何满足贪心解。如果读者有能进行贪心证明，请务必教教我！

方法二 Kruscal做法
从大到小枚举每一条边，第一次1和n连通的时候就是我们要找的最小值最大解。

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
const int N = 1010, M = N * N;

struct Edge
{
    int u, v, d;
    Edge(){}
    Edge(int u, int v, int d):u(u),v(v),d(d){}
}edge[M];

bool cmp(Edge a, Edge b)
{return a.d > b.d;}

int n, m;
int f[N];

int find(int x)
{
    if(f[x] != x) f[x] = find(f[x]);
    return f[x];
}

int main()
{
    int T;
    cin >> T;

    for(int t = 1; t <= T; t ++)
    {
        cin >> n >> m;

        // init
        for(int i = 0; i < N; i ++)
            f[i] = i;

        for(int i = 0; i < m; i ++)
        {
            int a, b, c;
            scanf("%d%d%d",&a,&b,&c);
            edge[i] = Edge(a,b,c);
        }

        sort(edge,edge + m,cmp);
        int res = 0;
        for(int i = 0; i < m; i ++)
        {
            int a, b, w;
            a = edge[i].u, b = edge[i].v, w = edge[i].d;

            a = find(a), b = find(b);
            if(a != b)
            {
                // 把彼此的祖宗节点都给连上
                f[a] = b;
            }
            // 第一次连通的时候就是最小值最大
            if(find(1) == find(n))
            {
                res = w;
                break;
            }
        }

        printf("Scenario #%d:\n",t);
        cout << res << endl;
        puts("");
    }
    
    return 0;
}
```