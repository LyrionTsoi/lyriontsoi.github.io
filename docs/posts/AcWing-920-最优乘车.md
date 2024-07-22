---
title: AcWing 920. 最优乘车
date: 2022-08-11 19:41:40
tags:
- 输入问题
- 建图
- 最短路
categories:
- AcWing
- 最短路
---
## [AcWing 920. 最优乘车](https://www.acwing.com/problem/content/342/)

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
0≤K<N≤1000,
1≤P≤10000,
1≤Li≤1000000
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

### 题解
本题难题在于输入以及建图的层面

输入使用sstream(按行读入，甚至是能读入换行符号的)
```C++
    string line;
    getline(cin, line); // 把第一行的换行符给清空
    for(int i = 1; i <= n; i ++)
    {
        getline(cin, line);
        stringstream ssin(line);
        int p, cnt = 0;
        // 因为要求换乘次数最少所以应该同一条线路上的所有点都是相通的
        while(ssin >> p) stop[cnt ++ ] = p;
    }
```

建图
由于题目要求我们求出来换成次数最少所以在建图上一条线路上的所有站两两之间都是的相连通的

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
const int N = 510, M = N * N;

// 边权为一，bfs可以求最小换成次数

int n,m;
int h[N], e[M], ne[M], idx;
int dist[N], stop[N];

void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

void bfs()
{
    int hh = 0, tt = -1;
    int q[N];
    q[++ tt] = 1;
    dist[1] = 0;

    while(hh <= tt)
    {
        int t = q[hh ++];

        for(int i = h[t]; ~i; i = ne[i])
        {
            int j = e[i];
            if(dist[j] > dist[t] + 1)
            {
                dist[j] = dist[t] + 1;
                q[++ tt] = j;
            }
        }
    }
}

int main()
{
    cin >> n >> m;

    memset(dist, 0x3f, sizeof dist);
    memset(h, -1, sizeof h);

    string line;
    getline(cin, line); // 把第一行的换行符给清空
    for(int i = 1; i <= n; i ++)
    {
        getline(cin, line);
        stringstream ssin(line);
        int p, cnt = 0;
        // 因为要求换乘次数最少所以应该同一条线路上的所有点都是相通的
        while(ssin >> p) stop[cnt ++ ] = p;

        for(int i = 0; i < cnt; i ++)
            for(int j = i + 1; j < cnt; j ++)
                add(stop[i],stop[j]);
    }

    bfs();

    if(dist[m] == INF) cout << "NO" << endl;
    else cout << max(dist[m] - 1, 0);

    return 0;
}
```