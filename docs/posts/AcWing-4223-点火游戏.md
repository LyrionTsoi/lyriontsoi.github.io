---
title: AcWing 4223. 点火游戏
date: 2022-10-25 11:36:35
tags:
- kuangbing
- 搜索
- 双路bfs
categories:
- kuangbing
- 简单搜索
- 双路bfs
---

## [AcWing 4223. 点火游戏](https://www.acwing.com/problem/content/4226/)
给定一个 N 行 M 列的方格矩阵。

其中一部分方格是草地，其余部分是空地。

草地能够被燃烧，空地不会。

当某个草地在 t 时刻被点燃时，其上下左右四个方向的相邻方格中的草地方格也会在 t+1 时刻被点燃。

注意，空地方格无论如何都不可能被点燃。

现在，你可以选择最多两个草地，将它们点燃。

请你计算，使得所有草地都被点燃所需花费的最少时间。

输入格式
第一行包含整数 T，表示共有 T 组测试数据。

每组数据第一行包含两个整数 N,M。

接下来 N 行，包含一个 N×M 的字符矩阵，# 表示草地，. 表示空地。

输出格式
每组数据输出一个结果，每个结果占一行。

结果表示为 Case x: y，其中 x 为组别编号（从 1 开始），y 点燃所有草地需要花费的最短时间。如果无法点燃所有草地或者所有方格都是空地则输出 −1。

数据范围
1≤T≤100,
1≤N,M≤10。

输入样例：
```
5
3 3
.#.
###
.#.
3 3
.#.
#.#
.#.
3 3
...
#.#
...
3 3
###
..#
#.#
3 3
...
...
...
```
输出样例：
```
Case 1: 1
Case 2: -1
Case 3: 0
Case 4: 2
Case 5: -1
```

### 题解
本题是多起点问题的bfs，并且这两个起点是相互影响的。

最常见的错误是构造虚拟源点，实际上因为起点之间是具有依赖关系的，

第二个方法考虑，创建两个队列，进行双路bfs。这里的双路bfs是根据双向bfs来写的。
这里又错了，这是为什么了？？因为两个起点可能会遍历到同一个起点，又应为每个点只能被遍历一次所以这个当前两个起点都可以到达的点的这dist值要求两个前驱点的最小值。如果这里借一个map，两个队列都有自个的一个map，这样可以吗？？答案还是不行的，因为这个具有多个前驱点的这个点，有可能在两个队列相遇之前的就已经开始拓展了影响了其他点，所以这里使用两个队列依然不行。

是的以上办法我都试过了都错误了。。。还是太菜了。。。。

最后的办法，使用一个队列，把两个起点都放入到同一个队列当中，构造一个结构体node，记录的是当前的步长，具体看代码，和正常的bfs很像，就是一开始入队的时候进入了两个起始点。

本题还有一个很坑的地方，可能双起点会在同一个位置上。

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
const int N = 15;

struct node
{
    int x, y;
    int dist;
};

int n, m,cnt, res;
char g[N][N];
queue<node> q;
bool vis[N][N];
int dx[] = {1,-1,0,0},dy[] = {0,0,1,-1};

int extend(queue<node> &q, int &block)
{
    // cout << 2 << endl; 
    auto t = q.front();
    q.pop();
    int x = t.x, y = t.y, dist = t.dist;
    // cout << x  << ' ' << y << endl;
    int output = dist;
    for(int i = 0; i < 4; i ++)
    {
        int cx = x + dx[i], cy = y + dy[i];
        if(cx > 0 && cx <= n && cy > 0 && cy <= m && !vis[cx][cy] && g[cx][cy] == '#')
            q.push({cx,cy,dist + 1}), block --, output = dist + 1, vis[cx][cy] = true;
    }
    return output;
}

int bfs()
{
    int block = cnt - 2;
    int ans = 0;
    while(q.size())
    {
        // cout << 1 << endl;
        int t;
        if(!block) break;
        t = extend(q, block);
        ans = max(ans, t);
    }

    if(block) return -1;
    else return ans;
}

void solve()
{
    // 枚举起点
    res = INF;
    // 枚举起点的时候要考虑两个起点可能是同一个起点
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= m; j ++)
            for(int u = 1; u <= n; u ++)
                for(int v = 1; v <= m; v ++)
                    if(g[i][j] == '#' && g[u][v] == '#')
                    {
                        // bfs前初始化
                        // 清空队列
                        // 初始化状态数组
                        queue<node> emp;
                        swap(emp,q);
                        memset(vis, 0, sizeof vis);

                        // cout << i << ' ' << j << endl;
                        // cout << u << ' ' << v << endl;
                        // 这里如果是同一个起点也无所谓，因为拓展了一个节点另外一个节点就不可能被拓展了
                        q.push({i,j,0}), q.push({u,v,0});
                        vis[i][j] = 1, vis[u][v] = 1;
                        int t = bfs();
                        // cout << t << endl;
                        if(t != -1) res = min(res ,t);
                    }
}

int main()
{
    int T;
    cin >> T;

    for(int t = 1; t <= T; t ++)
    {
        cin >> n >> m;
        cnt = 0;
        for(int i = 1; i <= n; i ++)
            for(int j = 1; j <= m; j ++)
            {    
                cin >> g[i][j];
                if(g[i][j] == '#') cnt ++;
            }

        solve();

        if(cnt == 0) res = -1;
        else if(cnt <= 2) res = 0;
        else if(res == INF) res = -1;

        printf("Case %d: %d\n", t, res);
    }
    return 0;
}
```