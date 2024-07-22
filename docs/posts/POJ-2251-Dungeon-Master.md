---
title: POJ-2251 Dungeon Master
date: 2022-10-16 11:11:03
tags:
- 搜索
- DFS
categories:
- POJ
- 搜索
---

## [POJ-2251 Dungeon Master](https://vjudge.net/problem/POJ-2251#author=StelaYuri)
You are trapped in a 3D dungeon and need to find the quickest way out! The dungeon is composed of unit cubes which may or may not be filled with rock. It takes one minute to move one unit north, south, east, west, up or down. You cannot move diagonally and the maze is surrounded by solid rock on all sides.

Is an escape possible? If yes, how long will it take?
你被困在一个 L\times R\times CL×R×C 的三维迷宫内，每分钟你只能够往上、下、左、右、前、后这六个方向的任意一个方向走一个单位。初始时你位于 SS 位置，迷宫的出口为 EE 位置，迷宫中标 \## 的位置无法通过，并且你也不能够走出迷宫的边界。

你是否能够走出这个迷宫？如果能，求出最少的时间。

Input
The input consists of a number of dungeons. Each dungeon description starts with a line containing three integers L, R and C (all limited to 30 in size).
L is the number of levels making up the dungeon.
R and C are the number of rows and columns making up the plan of each level.
Then there will follow L blocks of R lines each containing C characters. Each character describes one cell of the dungeon. A cell full of rock is indicated by a '#' and empty cells are represented by a '.'. Your starting position is indicated by 'S' and the exit by the letter 'E'. There's a single blank line after each level. Input is terminated by three zeroes for L, R and C.
迷宫有 L 层，每层长宽为 $R\times C。(L,R,C\le 30)$

输入有多组，以 L=R=C=0 结束。

Output
Each maze generates one line of output. If it is possible to reach the exit, print a line of the form Escaped in x minute(s). where x is replaced by the shortest time it takes to escape.
If it is not possible to escape, print the line Trapped!
Sample
Input
```
3 4 5
S....
.###.
.##..
###.#

#####
#####
##.##
##...

#####
#####
#.###
####E

1 3 3
S##
#E#
###

0 0 0
```
Output
```
Escaped in 11 minute(s).
Trapped!
```

### 题解
对于迷宫问题或者说使用的是dx/dy 这类数组涉及到-1/+1操作的情况，存储的边界建议的是从1开始存储，否则很容易SF错误

对于像POJ这样的老的不行的评测机
- 写结构体时候务必写构造函数并带上默认值。
- 不能使用auto
- 不能直接使用{x,y}这类，必须使用构造函数

### CODE
```
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
const int N =35, M = N * N * N;

struct loc
{
    int x,y,z;
    loc(int x = 0, int y = 0, int z = 0):x(x), y(y), z(z){}
}q[M];

char g[N][N][N];
int dx[] = {0,0,1,-1,0,0}, dy[] = {0,0,0,0,1,-1}, dz[] = {1,-1,0,0,0,0};
int dist[N][N][N];
int x,y,z;
loc st, ed;

int bfs(int sx, int sy, int sz)
{
    memset(dist, -1, sizeof dist);
    int hh = 0, tt = 0;
    q[0] = loc(sx,sy,sz);
    dist[sy][sx][sz] = 0;

    while(hh <= tt)
    {
        loc t = q[hh ++];

        for(int i = 0; i < 6; i ++)
        {
            int cx = t.x + dx[i], cy = t.y + dy[i], cz = t.z + dz[i];
            if(cx <= 0 || cx > x || cy <= 0 || cy > y || cz <= 0 || cz > z) continue;
            if(~dist[cy][cx][cz] || g[cy][cx][cz] == '#') continue;
            q[++ tt] = loc(cx,cy,cz);
            dist[cy][cx][cz] = dist[t.y][t.x][t.z]  + 1;
        }
    }
    return dist[ed.y][ed.x][ed.z];
}

int main()
{
    while(cin >> y >> x >> z, y && x && z)
    {
        for(int i = 1; i <= y; i ++)
        {
            for(int j = 1; j <= x; j ++)
                for(int k = 1; k <= z; k ++)
                {
                    cin >> g[i][j][k];
                    if(g[i][j][k] == 'S')
                        st = loc(j,i,k);
                    if(g[i][j][k] == 'E')
                        ed = loc(j,i,k);
                }
            getchar();
        }

        if(st.x == ed.x && st.y == ed.y && st.z == ed.z) 
            printf("Escaped in 0 minute(s).\n");
        else
        {
            int ans = bfs(st.x,st.y,st.z);
            if(ans == -1) puts("Trapped!");
            else printf("Escaped in %d minute(s).\n",ans);
        }
    }

    return 0;
}
```
 