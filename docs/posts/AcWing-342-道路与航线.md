---
title: AcWing 342 道路与航线
date: 2022-09-02 20:58:39
tags:
- 图论
- 最短路
categories:
- AcWing
- 图论
- 最短路
- 多种最短路算法结合
---

## [AcWing 342 道路与航线](https://www.acwing.com/problem/content/342/)

在郊区有 N 座通信基站，P 条 双向 电缆，第 i 条电缆连接基站 Ai 和 Bi。

特别地，1 号基站是通信公司的总站，N 号基站位于一座农场中。

现在，农场主希望对通信线路进行升级，其中升级第 i 条电缆需要花费 Li。

电话公司正在举行优惠活动。

农产主可以指定一条从 1 号基站到 N 号基站的路径，并指定路径上不超过 K 条电缆，由电话公司免费提供升级服务。

农场主只需要支付在该路径上剩余的电缆中，升级价格最贵的那条电缆的花费即可。

求至少用多少钱可以完成升级。

输入格式
第 1 行：三个整数 N，P，K。

第 2..P+1 行：第 i+1 行包含三个整数 Ai,Bi,Li。

输出格式
包含一个整数表示最少花费。

若 1 号基站与 N 号基站之间不存在路径，则输出 −1。

数据范围
0 ≤ K < N ≤ 1000,
1 ≤ P ≤ 10000,
1 ≤ Li ≤ 1000000
输入样例：
```
5 7 1
1 2 5
3 1 4
2 4 8
3 2 3
5 2 9
3 4 7
4 5 6
```
输出样例：
```
4
```

## 题解
题目告诉
* 道路全为整数且为双向边
* 航线单向边且边权有正有负但是包含航线的路线是不具有环的（把道路团看成结点后就是是一个拓扑图）

对于本题我们不难看出来的本题是一道单源最短路问题且带有边权为负数，**但是sfpa会被卡成nm = 1e9的级别所以不可以用spfa算法**

细节处理
1. 证明道路可以看成一个团（团内没有航线）
 反证法：如果团内有航线，由于道路是具有双向边的所以相邻的两点之间一定是相通的，这样就和航线两端是不存在环的，所以一定存在一个团内部的点都有且仅有道路连通
2. 思路上我们把团缩成一个点，大体上做拓扑排序问题内部做dijksra算法问题
3. 如何把全部点分成一个一个团
 通过dfs/bfs/并查集，我们把通过道路连通的点分成一个团，最后就可以得到不同的团
4. 如何将拓扑序和dijkstra结合起来
 Step1.读入所有的道路，用dfs算法将所有的道路分成团并对团进行编号
 Step2.读入所有的航线并记录所有点的入读
 Step3.做topsort（）
 Step4.把所有入度为0的团加入到堆当中，然后对团内部做dijkstra算法
5. dijkstra算法
 当前的dijkstra算法要平时的不同，对点的邻点做更新。如果id[i] == id[j]说明这两个点在同一个团当中，如果可以更新就进行更新，然后加入到堆当中，如果id[i] != id[j]的话，说明不在同一个团当中通过航相连，并对bock[id[j]]的入度点做减一更新


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
const ll mod=1000000007;
const int INF = 0x3f3f3f3f;
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head
const int N = 25010, M = 50000 * 3 + 10;

int n,m,p,S;
int h[N],e[M], w[M], ne[M], idx;
int bcnt;
int id[N], bin[N], dist[N]; // id[x]表示x点在那个团当中 
bool st[N];
vector<int> block[N];   // 保存是每个团当中的结点编号
queue<int> q;


void add(int a, int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx ++;
}

void dfs(int u, int bid)
{
    // 对id和block数组进行初始化
    id[u] = bid, block[bid].pb(u);

    for(int i = h[u]; ~i; i = ne[i])
    {
        int j = e[i];
        if(!id[j])
            dfs(j,bid);
    }
}

void dijkstra(int bid)
{
    // 建立heap
    priority_queue<PII,vector<PII>, greater<PII>> heap;

    for(auto u : block[bid])
        heap.push({dist[u], u});

    while(heap.size())
    {
        auto t = heap.top();
        heap.pop();

        int ver = t.second;
        if(st[ver]) continue;   // 每个点只会被更新一次 
        st[ver] = true;

        for(int i = h[ver]; ~i; i = ne[i])
        {
            int j = e[i];
            if(dist[j] > dist[ver] + w[i])
            {
                dist[j] = dist[ver] + w[i];
                if(id[ver] == id[j])
                    heap.push({dist[j], j});
            }
            if(id[j] != id[ver] && -- bin[id[j]] == 0)
                q.push(id[j]);
        }

    }

}

void topsort()
{
    // init
    memset(dist, 0x3f, sizeof dist);
    memset(st, false, sizeof st);
    dist[S] = 0;

    for(int i = 1; i <= bcnt; i ++)
        if(!bin[i]) q.push(i);

    while(q.size())
    {
        auto t = q.front();
        q.pop();
        dijkstra(t);
    }
}

int main()
{
    cin >> n >> m >> p >> S;

    // 通过道路建图
    memset(h,-1,sizeof h);
    for(int i = 0; i < m; i ++)
    {
        int a, b, c;
        scanf("%d%d%d",&a,&b,&c);
        add(a,b,c),add(b,a,c);
    }

    // 建立团
    for(int i = 1; i <= n ; i ++)
        if(!id[i])
            dfs(i, ++ bcnt);

    // 读入航线 
    for(int i = 0; i < p; i ++)
    {
        int a, b, c;
        scanf("%d%d%d",&a,&b,&c);
        add(a,b,c);
        bin[id[b]] ++;
    }

    topsort();

    // 输出
    for(int i  = 1; i <= n; i ++)
        if(dist[i] > INF / 2) cout << "NO PATH" << endl;
        else cout << dist[i] << endl;
    
    return 0;
}
```
  

