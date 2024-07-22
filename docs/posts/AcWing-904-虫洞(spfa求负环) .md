---
title: AcWing 904. 虫洞(spfa求负环) 
date: 2022-11-07 20:06:25
tags:
- 最短路
- 负环
categories:
- AcWing
- 图论
- 负环
---

# [AcWing 904. 虫洞(spfa求负环)](https://www.acwing.com/problem/content/906/)

农夫约翰在巡视他的众多农场时，发现了很多令人惊叹的虫洞。

虫洞非常奇特，它可以看作是一条 单向 路径，通过它可以使你回到过去的某个时刻（相对于你进入虫洞之前）。

农夫约翰的每个农场中包含 N 片田地，M 条路径（双向）以及 W 个虫洞。

现在农夫约翰希望能够从农场中的某片田地出发，经过一些路径和虫洞回到过去，并在他的出发时刻之前赶到他的出发地。

他希望能够看到出发之前的自己。

请你判断一下约翰能否做到这一点。

下面我们将给你提供约翰拥有的农场数量 F，以及每个农场的完整信息。

已知走过任何一条路径所花费的时间都不超过 10000 秒，任何虫洞将他带回的时间都不会超过 10000 秒。

输入格式
第一行包含整数 F，表示约翰共有 F 个农场。

对于每个农场，第一行包含三个整数 N,M,W。

接下来 M 行，每行包含三个整数 S,E,T，表示田地 S 和 E 之间存在一条路径，经过这条路径所花的时间为 T。

再接下来 W 行，每行包含三个整数 S,E,T，表示存在一条从田地 S 走到田地 E 的虫洞，走过这条虫洞，可以回到 T 秒之前。

输出格式
输出共 F 行，每行输出一个结果。

如果约翰能够在出发时刻之前回到出发地，则输出 YES，否则输出 NO。

数据范围
1≤F≤5
1≤N≤500,
1≤M≤2500,
1≤W≤200,
1≤T≤10000,
1≤S,E≤N
输入样例：
```
2
3 3 1
1 2 2
1 3 4
2 3 1
3 1 3
3 2 1
1 2 3
2 3 4
3 1 8
```
输出样例：
```
NO
YES
```

## 题解
本题是需要判断是图上是否有负环而不是找出所有的负环中的最小

构造一个虚拟源点与当前图上的每一个点相连，统计当前每个点的最短路中所包含的边数，如果某点的最短路所包含的边数大于等于n，则说明存在环

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
const int N = 510, M = 5300;

int n, m, wh;
int h[N], e[M], ne[M], w[M], idx;
int dist[N], cnt[N];
int q[N];
bool st[N]; // 判断这个点是否在队列当中


void add(int a, int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx ++;
}

bool spfa()
{
    // dist 距离源点最短距离， cnt从源点到这个点有多少条路径
    int hh = 0, tt = 0;
    memset(dist, 0, sizeof dist);
    memset(cnt, 0, sizeof cnt);
    memset(st, 0, sizeof st);
    for(int i = 1; i <= n; i ++)
        q[tt ++] = i, st[i] = 1;

    while(hh != tt)
    {
        int t = q[hh ++];
        if(hh == N) hh = 0;
        st[t] = false;

        for(int i = h[t]; ~i; i = ne[i])
        {
            int j = e[i];
            if(dist[j] > dist[t] + w[i])
            {
                dist[j] = dist[t] + w[i];
                cnt[j] = cnt[t] + 1;
                if(cnt[j] >= n) return true;
                if(!st[j])
                {
                    q[tt ++] = j;
                    if(tt == N) tt = 0;
                    st[j] = true;
                }
            }
        }
    }
    return false;
}

int main()
{
    int T;
    cin >> T;

    while(T --)
    {
        cin >> n >> m >> wh;

        memset(h, -1, sizeof h);
        idx = 0;
        for(int i = 0; i < m; i ++)
        {
            int a, b, c;
            cin >> a >> b >> c;
            add(a,b,c);
            add(b,a,c);
        }

        for(int i = 0; i < wh; i ++)
        {
            int a, b, c;
            cin >> a >> b >> c;
            c = -c;
            add(a,b,c);
        }

        if(spfa()) puts("YES");
        else puts("NO");
    }

    return 0;
}
```