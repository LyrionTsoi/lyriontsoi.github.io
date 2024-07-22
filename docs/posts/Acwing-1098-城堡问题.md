---
title: Acwing 1098. 城堡问题
date: 2022-11-01 15:21:56
tags:
- Flood Fill
- 连通块问题
- 搜索
categories:
- AcWing
- 搜索
- Flood Fill
---

## [Acwing 1098. 城堡问题](https://www.acwing.com/problem/content/1100/) 
```
1   2   3   4   5   6   7  
   #############################
 1 #   |   #   |   #   |   |   #
   #####---#####---#---#####---#
 2 #   #   |   #   #   #   #   #
   #---#####---#####---#####---#
 3 #   |   |   #   #   #   #   #
   #---#########---#####---#---#
 4 #   #   |   |   |   |   #   #
   #############################
           (图 1)

   #  = Wall   
   |  = No wall
   -  = No wall

   方向：上北下南左西右东。
```
图1是一个城堡的地形图。

请你编写一个程序，计算城堡一共有多少房间，最大的房间有多大。

城堡被分割成 m∗n个方格区域，每个方格区域可以有0~4面墙。

注意：墙体厚度忽略不计。

输入格式
第一行包含两个整数 m 和 n，分别表示城堡南北方向的长度和东西方向的长度。

接下来 m 行，每行包含 n 个整数，每个整数都表示平面图对应位置的方块的墙的特征。

每个方块中墙的特征由数字 P 来描述，我们用1表示西墙，2表示北墙，4表示东墙，8表示南墙，P 为该方块包含墙的数字之和。

例如，如果一个方块的 P 为3，则`3 = 1 + 2`，该方块包含西墙和北墙。

城堡的内墙被计算两次，方块(1,1)的南墙同时也是方块(2,1)的北墙。

输入的数据保证城堡至少有两个房间。

输出格式
共两行，第一行输出房间总数，第二行输出最大房间的面积（方块数）。

数据范围
1≤m,n≤50,
0≤P≤15
输入样例：
```
4 7 
11 6 11 6 3 10 6 
7 9 6 13 5 15 5 
1 10 12 7 13 7 5 
13 11 10 8 10 12 13 
```
输出样例：
```
5
9
```

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
const int N = 55, M = N * N;

int n, m, cnt_block, area;
int g[N][N];
// int dist[N][N];
bool st[N][N];
int dx[] = {0,-1,0,1}, dy[] = {-1,0,1,0};

int bfs(int x, int y)
{
    int hh = 0, tt = 0;
    PII q[M];
    q[0] = {x,y};
    st[x][y] = true;

    int res = 0;
    while(hh <= tt)
    { 
        auto t = q[hh ++];
        res ++;
        int sx = t.fi, sy = t.se;

        for(int i = 0; i < 4; i++)
        {
            int cx = sx + dx[i], cy = sy + dy[i];
            // 判断这个是否越界这是为了防止遍历数组的时候出现遍历到-1这些位置造成了SF错误
            if(cx < 0 || cx >= n || cy < 0 || cy >= m) continue;
            // 如果当前没有门的情况下是可以更新状态的
            if(g[sx][sy] >> i & 1 || st[cx][cy]) continue;

            q[++ tt] = {cx, cy};
            st[cx][cy] = 1;
        }
    }

    return res;
}

int main()
{
    cin >> n >> m;

    for(int i = 0; i < n;i ++)
        for(int j = 0; j < m; j ++)
            cin >> g[i][j];

    for(int i = 0; i < n; i ++)
        for(int j = 0; j < m; j ++)
        {
            if(!st[i][j])
            {
                int t = bfs(i,j);
                if(t != 0)
                {
                    cnt_block ++;
                    area = max(area, t);
                }
            }
        }

    cout << cnt_block << endl << area << endl;

    return 0;
}
```
