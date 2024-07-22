---
title: AcWing 1097. 池塘计数(模版)
date: 2022-10-11 16:14:58
tags:
- 模版
- 搜索
- Flood fill
categories:
- AcWing
- - 搜索
- Flood fill
---

##  [AcWing 1097. 池塘计数(模版)](https://www.acwing.com/problem/content/1099/)
农夫约翰有一片 N∗M 的矩形土地。

最近，由于降雨的原因，部分土地被水淹没了。

现在用一个字符矩阵来表示他的土地。

每个单元格内，如果包含雨水，则用”W”表示，如果不含雨水，则用”.”表示。

现在，约翰想知道他的土地中形成了多少片池塘。

每组相连的积水单元格集合可以看作是一片池塘。

每个单元格视为与其上、下、左、右、左上、右上、左下、右下八个邻近单元格相连。

请你输出共有多少片池塘，即矩阵中共有多少片相连的”W”块。

输入格式
第一行包含两个整数 N 和 M。

接下来 N 行，每行包含 M 个字符，字符为”W”或”.”，用以表示矩形土地的积水状况，字符之间没有空格。

输出格式
输出一个整数，表示池塘数目。

数据范围
$1≤N,M≤1000$
输入样例：
```
10 12
W........WW.
.WWW.....WWW
....WW...WW.
.........WW.
.........W..
..W......W..
.W.W.....WW.
W.W.W.....W.
.W.W......W.
..W.......W.
```
输出样例：
```
3
```

### 题解
本题有几个点
* PII q[M] 这个队列不能在局部变量下声请，最坏的情况下会进行$1e^6$声请，实测会T。

**对st[]数组的总结**
**BFS算法**，理论上bfs是不会走回头路的，所以如果只做一次bfs是不用st数组的。但是这里需要多次bfs所以还需要记录每个坐标的状态。

**Dijkstra algorithm（堆优化）**， 理论上Dijkstra是bfs的一种优化。所以该算法的写法是一种类bfs写法。st数组表示的是这个点是否被更新（因为和bfs的原理相似，所以每一个点只会被且有且只能被更新一次，所以不能重新更新）

**SPFA Algorithm**, st数组记录的是当前的这个节点是否在队列内部。
每次的bfs都是走不出当前所在的连通块的，所以需要多次的bfs
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
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head
const int N = 1010, M = N * N;

char g[N][N];
bool st[N][N];
int n,m;
PII q[M];


void bfs(int x, int y)
{
    int hh = 0, tt = -1;
    q[++ tt] = {x,y};
    st[x][y] = true;

    while(hh <= tt)
    {
        auto t = q[hh ++];

        // 因为这里是八联通问题所以最好还是使用枚举点的方式，虽然也可以使用dx/dy数组但是要写八个点对比较麻烦
        for(int i = t.fi - 1; i <= t.fi + 1; i ++)
            for(int j = t.se - 1; j <= t.se + 1; j ++)
            {
                // 特判中心点
                if(i == t.fi && j == t.se) continue;
                // 特判出界的变量
                if(i < 0 || i >= n || j < 0 || j >= m) continue;
                if(g[i][j] == 'W' && !st[i][j])
                {
                    q[++ tt] = {i,j};
                    st[i][j] = true;
                } 
            }
    }
}

int main()
{
    cin >> n >> m;

    for(int i = 0; i < n; i ++)
        scanf("%s",g[i]);

    int res = 0;
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < m; j ++)
        {
            if(g[i][j] == 'W' && !st[i][j])
            {
                bfs(i,j);
                res ++;
            }
        }

    cout << res << endl;

    return 0;
}
```
