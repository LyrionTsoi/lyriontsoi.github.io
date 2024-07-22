---
title: AcWing 1134. 最短路计数
date: 2022-09-06 10:29:46
tags:
- 图论
- 最短路
categories:
- AcWing
- 图论
- 最短路
- 与dp结合
---

## [AcWing 1134. 最短路计数](https://www.acwing.com/problem/content/1136/)
给出一个 N 个顶点 M 条边的无向无权图，顶点编号为 1 到 N。

问从顶点 1 开始，到其他每个点的最短路有几条。

输入格式
第一行包含 2 个正整数 N,M，为图的顶点数与边数。

接下来 M 行，每行两个正整数 x,y，表示有一条顶点 x 连向顶点 y 的边，请注意可能有自环与重边。

输出格式
输出 N 行，每行一个非负整数，第 i 行输出从顶点 1 到顶点 i 有多少条不同的最短路，由于答案有可能会很大，你只需要输出对 100003 取模后的结果即可。

如果无法到达顶点 i 则输出 0。

数据范围
1≤N≤105,
1≤M≤2×105
输入样例：
```
5 7
1 2
1 3
2 4
3 4
2 3
4 5
4 5
```
输出样例：
```
1
1
1
2
4
```

## 题解
根据dp来划分状态并记录最小花费的所有方案
 1. 先算出全局最小值
 2. 分别求出每个子集中等于全局最小值的元素个数

这里说明为什么在dp问题我们可以使用循环的方式来更新，而最短路不行
 1.dp能使用循环的方式，是因为当前状态前面的状态已经计算完毕，所以可以利用前面状态来更新当前的状态。这也说明了只有当状态转移不具有依赖关系的时候而具有拓扑序的时候，才可以利用dp方式来求解

最短路算法中即使图中有环也可以将环图转化拓扑图(最短路树) -> 转化为拓扑图之后就可以使用dp求解（图中有负环或零环的情况下求方案数）
 由于dist[j] = min(dist[j], dist[t] + w[i])所以每一个点都一定会被前驱节点更新，有可能会被一个节点更新有可能会被多个节点更新，我们只需要要保留一个/多个更新当前节点的前驱节点就可以转化成一个拓扑图

 **在这里要注意**， 如果当边权为0的时候，说明当前两个状态之间其实等价的关系，本质上可以相互转化的，这时候就会存在一个权重为0的环，可以在这个环上绕无限圈，所以这个时候是不能使用最短路来求的（等价于图上有负环的时候是不能通过最短路来求）

 由于图上没有负权边，所以如果图上存在一个环，说明这个环一定是边权为0的环（可以通过dist[i] = dist[j] + w[u] == dist[j] = dist[k] + w[v] == dist[k] = dist[i] + w[p] 算出来)

总结：
* 只要图中没有负权图的dp问题即使有正环都是可以使用最短路的方式来求解的。如有有负权边的情况，就需要判断是否有负环，或则能否转化成拓扑图的形式。
* 如果图中含有负环（或零环）的情况下，使用spfa求解。
 1. 先用spfa跑出来最短距离
 2. 建立拓扑树
 3. 用dist[j] = dist[t] + w[i]判断是否成立，成立就更新，但必须满足t已经完全更新过。


## CODE
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
const ll mod=100003;
const int INF = 0x3f3f3f3f;
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head
const int N = 100010, M = 400010;

int h[N], e[M], ne[M], idx;
int n, m;
int dist[N], cnt[N], q[N];

void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

void bfs()
{
    memset(dist, 0x3f, sizeof dist);

    int hh = 0, tt = 1;
    q[hh] = 1;
    dist[1] = 0;
    cnt[1] = 1;

    while(hh <= tt)
    {
        int t = q[hh ++];

        for(int i = h[t]; ~i; i = ne[i])
        {
            int j = e[i];

            if(dist[j] > dist[t] + 1)
            {
                dist[j] = dist[t] + 1;
                cnt[j] = cnt[t];
                q[++ tt] = j;
            }
            else if(dist[j] == dist[t] + 1) // 最短路有多条
            {
                cnt[j] = (cnt[j] + cnt[t]) % mod;
            }
        }

    }
}

int main()
{
    cin >> n >> m;

    // build
    memset(h, -1, sizeof h);
    for(int i = 0; i < m; i ++)
    {
        int a, b;
        scanf("%d%d",&a,&b);
        // 题意可能有点问题应该所有边都是无向边
        // 5 5
        // 1 2  
        // 2 3
        // 3 4
        // 4 1
        // 4 5
        add(a,b);
        add(b,a); 
    }

    // 最短路计数
    bfs();

    for(int i = 1; i <= n; i ++)
        cout << cnt[i] << endl;

    return 0;
}
```