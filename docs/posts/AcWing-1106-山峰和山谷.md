---
title: AcWing 1106. 山峰和山谷
date: 2022-11-01 21:21:44
tags:
- Flood Fill
- 连通块
categories:
- AcWing
- 搜索
- Flood Fill
---

## [AcWing 1106. 山峰和山谷](https://www.acwing.com/problem/content/1108/)
FGD小朋友特别喜欢爬山，在爬山的时候他就在研究山峰和山谷。

为了能够对旅程有一个安排，他想知道山峰和山谷的数量。

给定一个地图，为FGD想要旅行的区域，地图被分为 n×n 的网格，每个格子 (i,j) 的高度 w(i,j) 是给定的。

若两个格子有公共顶点，那么它们就是相邻的格子，如与 (i,j) 相邻的格子有(i−1,j−1),(i−1,j),(i−1,j+1),(i,j−1),(i,j+1),(i+1,j−1),(i+1,j),(i+1,j+1)。

我们定义一个格子的集合 S 为山峰（山谷）当且仅当：

S 的所有格子都有相同的高度。
S 的所有格子都连通。
对于 s 属于 S，与 s 相邻的 s′ 不属于 S，都有 ws>ws′（山峰），或者 ws<ws′（山谷）。
如果周围不存在相邻区域，则同时将其视为山峰和山谷。
你的任务是，对于给定的地图，求出山峰和山谷的数量，如果所有格子都有相同的高度，那么整个地图即是山峰，又是山谷。

输入格式
第一行包含一个正整数 n，表示地图的大小。

接下来一个 n×n 的矩阵，表示地图上每个格子的高度 w。

输出格式
共一行，包含两个整数，表示山峰和山谷的数量。

数据范围
1≤n≤1000,
0≤w≤109
输入样例1：
```
5
8 8 8 7 7
7 7 8 8 7
7 7 7 7 7
7 8 8 7 8
7 8 8 8 8
```
输出样例1：
```
2 1
```
输入样例2：
```
5
5 7 8 3 1
5 5 7 6 6
6 6 6 2 8
5 7 2 5 8
7 1 0 1 7
```
输出样例2：
```
3 3
```

**样例解释**
样例1：
![样例1](https://cdn.acwing.com/media/article/image/2019/10/16/19_0250799aef-1.png)
样例2：
![样例2](https://cdn.acwing.com/media/article/image/2019/10/16/19_08db5e60ef-2.png)

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
const int N = 1010, M = N * N;

int n;
int g[N][N];
bool st[N][N];
PII q[M];

void bfs(int sx, int sy, int &higher, int &lower)
{
    int hh = 0, tt = 0;
    q[0] = {sx, sy};
    st[sx][sy] = 1;

    while(hh <= tt)
    {
        auto t = q[hh ++];

        int x = t.fi, y = t.se;
        // 八连通问题
        for(int i = x - 1; i <= x + 1; i ++)
            for(int j = y - 1; j <= y + 1; j ++)
            {
                if(i < 0 || i >= n || j < 0 || j >= n) continue;
                if(i == x && j == y) continue;
                // 如果不是同一个山脉就要查看这个位置的海拔与我们当前枚举位置海拔对比
                if(g[i][j] != g[x][y])
                {
                    if(g[i][j] > g[x][y]) higher ++;
                    if(g[i][j] < g[x][y]) lower ++;
                }
                else if(!st[i][j]) 
                {
                    q[++ tt] = {i,j};
                    st[i][j] = true;
                }
            }
    }
}

int main()
{
    cin >> n;

    for(int i = 0; i < n; i ++)
        for(int j = 0; j < n; j ++)
            cin >> g[i][j];

    // 同一个山脉表示的就是的同一个连通块
    // 用Flood Fill算法的进行BFS搜索
    int peak = 0, villey = 0;
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < n; j ++)
            if(!st[i][j])
            {
                int higher = 0, lower = 0;
                bfs(i,j,higher, lower);
                // 如果这个连通块（山脉）附近没有比他还要高的就说明这个是山峰
                if(!higher) peak ++;
                if(!lower) villey ++;
            }

    cout << peak << ' ' << villey << endl;

    return 0;
}
```