---
title: CodeForces-295B Greg and Graph
date: 2022-07-21 11:54:32
tags:
- floyd
- 最短路
catergories:
- CodeForces
- 图论
- 最短路
---

## [CodeForces-295B Greg and Graph](https://vjudge.net/problem/CodeForces-295B)
Greg has a weighed directed graph, consisting of n vertices. In this graph any pair of distinct vertices has an edge between them in both directions. Greg loves playing with the graph and now he has invented a new game:

The game consists of n steps.
On the i-th step Greg removes vertex number xi from the graph. As Greg removes a vertex, he also removes all the edges that go in and out of this vertex.
Before executing each step, Greg wants to know the sum of lengths of the shortest paths between all pairs of the remaining vertices. The shortest path can go through any remaining vertex. In other words, if we assume that d(i, v, u) is the shortest path between vertices v and u in the graph that formed before deleting vertex xi, then Greg wants to know the value of the following sum: .
Help Greg, print the value of the required sum before each step.

Input
The first line contains integer n (1 ≤ n ≤ 500) — the number of vertices in the graph.

Next n lines contain n integers each — the graph adjacency matrix: the j-th number in the i-th line aij (1 ≤ aij ≤ 105, aii = 0) represents the weight of the edge that goes from vertex i to vertex j.

The next line contains n distinct integers: x1, x2, ..., xn (1 ≤ xi ≤ n) — the vertices that Greg deletes.

Output
Print n integers — the i-th number equals the required sum before the i-th step.

Please, do not use the %lld specifier to read or write 64-bit integers in C++. It is preferred to use the cin, cout streams of the %I64d specifier.

### 题解
**本题需要深入理解floyd原理**
也就是说给我们每次要删去的点，每次都求一下两点之间的最短路。
要是我们每删一个点再去做一遍多源最短路的话，那么时间复杂度会变为O（n^4).
这里floyd算法要注意两点：
1. floyd本质上就是一个dp问题，状态转移方程为∑d[i][j]= min(d[i][j],d[i][k] + d[k][j]);
这里的状态方程可以表示为从i->j的最短距离，i到j这个路径上可以被分成了两段，i->k, k->j，那我们就可以得到我们的最优子结构了(这里的内层循环k）。
2. 实际上floyd也可以理解为选择一个顶点集作为顶点的中转站 S，假如第一次选了1号ver作为中转，那么我们的所有顶点都可以用1号顶点做为中转站更新距离。（这里是外层循环k）

那么本题问题我们就可以转化为加点问题（每次选一个点作为中转，等用于我们加点加入了S集合当中），逆向floyd

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
const int N = 510;

ll g[N][N];
int ver[N];
ll ans[N];
bool st[N];
int n; 

int main()
{
    while(cin >> n)
    {
        memset(g,0x3f, sizeof g);
        memset(st, 0, sizeof st);
        for(int i = 1; i <= n; i ++) g[i][i] = 0;
        for(int i = 1; i <= n; i ++)
            for(int j = 1; j <= n; j ++)
                scanf("%lld",&g[i][j]);

        for(int i = 1; i <= n; i ++)
            scanf("%d",&ver[i]);

        for(int k = n; k >= 1; k --)
        {
            int u = ver[k];
            // 因为我们要逆序进行floyd，所以我们在计算两点之间的最短距离之前要知道这两个点是否在这个图当中
            st[u] = true;
            for(int i = 1; i <= n; i ++)
                for(int j = 1; j <= n; j ++)
                    g[i][j] = min(g[i][j], g[i][u] + g[u][j]);

            ll res = 0;
            for(int i = 1; i <= n; i ++)
                for(int j = 1; j <= n; j ++)
                    if(st[i] && st[j])
                        res += g[i][j];
            // 题目要求在我们删点之前计算最长路径，那么逆序就变为加点之后计算最长路径 
            ans[k] = res;
        }
        
        for(int i = 1; i <= n; i ++)
            printf("%lld%c",ans[i],i==n?'\n':' ');
    }
    return 0;
}
```