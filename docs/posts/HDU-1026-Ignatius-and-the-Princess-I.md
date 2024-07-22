---
title: HDU-1026 Ignatius and the Princess I
date: 2022-07-21 15:04:12
tags:
- 最短路
- 输出最优解问题
- 优先队列bfs
catergories: 
- - 图论
- - 最短路
- HDU
---

## [HDU-1026 Ignatius and the Princess I](https://vjudge.net/problem/HDU-1026)
The Princess has been abducted by the BEelzebub feng5166, our hero Ignatius has to rescue our pretty Princess. Now he gets into feng5166's castle. The castle is a large labyrinth. To make the problem simply, we assume the labyrinth is a N*M two-dimensional array which left-top corner is (0,0) and right-bottom corner is (N-1,M-1). Ignatius enters at (0,0), and the door to feng5166's room is at (N-1,M-1), that is our target. There are some monsters in the castle, if Ignatius meet them, he has to kill them. Here is some rules:

1.Ignatius can only move in four directions(up, down, left, right), one step per second. A step is defined as follow: if current position is (x,y), after a step, Ignatius can only stand on (x-1,y), (x+1,y), (x,y-1) or (x,y+1).
2.The array is marked with some characters and numbers. We define them like this:
. : The place where Ignatius can walk on.
X : The place is a trap, Ignatius should not walk on it.
n : Here is a monster with n HP(1<=n<=9), if Ignatius walk on it, it takes him n seconds to kill the monster.

Your task is to give out the path which costs minimum seconds for Ignatius to reach target position. You may assume that the start position and the target position will never be a trap, and there will never be a monster at the start position.
`Input`
The input contains several test cases. Each test case starts with a line contains two numbers N and M(2<=N<=100,2<=M<=100) which indicate the size of the labyrinth. Then a N*M two-dimensional array follows, which describe the whole labyrinth. The input is terminated by the end of file. More details in the Sample Input.
`Output`
For each test case, you should output "God please help our poor hero." if Ignatius can't reach the target position, or you should output "It takes n seconds to reach the target position, let me show you the way."(n is the minimum seconds), and tell our hero the whole path. Output a line contains "FINISH" after each test case. If there are more than one path, any one is OK in this problem. More details in the Sample Output.

Sample
`Input`
```
5 6
.XX.1.
..X.2.
2...X.
...XX.
XXXXX.
5 6
.XX.1.
..X.2.
2...X.
...XX.
XXXXX1
5 6
.XX...
..XX1.
2...X.
...XX.
XXXXX.
```
`Output`
```
It takes 13 seconds to reach the target position, let me show you the way.
1s:(0,0)->(1,0)
2s:(1,0)->(1,1)
3s:(1,1)->(2,1)
4s:(2,1)->(2,2)
5s:(2,2)->(2,3)
6s:(2,3)->(1,3)
7s:(1,3)->(1,4)
8s:FIGHT AT (1,4)
9s:FIGHT AT (1,4)
10s:(1,4)->(1,5)
11s:(1,5)->(2,5)
12s:(2,5)->(3,5)
13s:(3,5)->(4,5)
FINISH
It takes 14 seconds to reach the target position, let me show you the way.
1s:(0,0)->(1,0)
2s:(1,0)->(1,1)
3s:(1,1)->(2,1)
4s:(2,1)->(2,2)
5s:(2,2)->(2,3)
6s:(2,3)->(1,3)
7s:(1,3)->(1,4)
8s:FIGHT AT (1,4)
9s:FIGHT AT (1,4)
10s:(1,4)->(1,5)
11s:(1,5)->(2,5)
12s:(2,5)->(3,5)
13s:(3,5)->(4,5)
14s:FIGHT AT (4,5)
FINISH
God please help our poor hero.
FINISH
```

### 题解
迷宫问题，求解`最短路`并且要`记录路径`，可以使用`优先队列BFS`（等价于dijkstra）
要清楚无论是bfs还是dijkstra每一个点只会被更新一次说一到一个点的前面那个点一定是唯一的，换言之到某个点的路径也一定是唯一的。所以要想记录路径则使用path[][]数组记录当前点前面一个点，然后从后往前推导路径。

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
#define x first
#define y second
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
const int N = 110;

char g[N][N];
int d[N][N];
int cost[N][N];
PII path[N][N];
int n,m;

struct node
{
    int x, y;
    int time;
    node(){}
    node(int x, int y, int time):x(x), y(y),time(time)
    {}
    bool operator < (const node&t)const
    {
        return time > t.time;
    }
};

void build()
{
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < m; j ++)
        {
            if(g[i][j] == '.')
                d[i][j] = 1;
            else if(g[i][j] == 'X')
                d[i][j] = INF;
            else 
                d[i][j] = g[i][j] - '0' + 1;
        }
    // puts("");
    // for(int i = 0; i < n; i ++)
    //     for(int j = 0; j < m; j ++)
    //         cout << d[i][j] << " \n"[j == m-1];
}

void bfs()
{
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < m; j ++)
            path[i][j] = {-1,-1};
    memset(cost, -1, sizeof cost);

    int dx[4] = {0,0,1,-1}, dy[4] = {1,-1,0,0};
    priority_queue<node> q;
    q.push({0,0,0});
    cost[0][0] = 0;


    while(!q.empty())
    {
        auto t = q.top();
        q.pop();

        if(t.x == n-1 && t.y == m-1) break;

        for(int i = 0; i < 4; i ++)
        {
            int nx = t.x + dx[i], ny = t.y + dy[i];
            if(nx < 0 || nx >= n || ny < 0 || ny >= m || d[nx][ny] == INF || cost[nx][ny] != -1) continue;
            cost[nx][ny] = cost[t.x][t.y] + d[nx][ny];
            path[nx][ny] = {t.x,t.y};
            // 入队是时间的花销，而不是d[nx][ny],如果入队的是当前节点的值，那么很显然不符合全局最优
            // 如果入队的是当前到这个节点的总时间花销，这样就可以一定是全局最优了
            q.push({nx,ny,cost[nx][ny]});   
        }
    }
}


int main()
{
    while(scanf("%d %d",&n,&m) != EOF)
    {
        for(int i = 0; i < n; i ++)
            scanf("%s",g[i]);

        build();    //先把图边权建立上
        bfs();

        if(cost[n-1][m-1] != -1)
        {
            cout << "It takes " << cost[n-1][m-1]<< " seconds to reach the target position, let me show you the way." << endl;
            vector<PII> ans;
            int tx = n - 1, ty = m - 1;
            ans.pb({tx,ty});
            while(tx != -1 && ty != -1)
            {
                int a = path[tx][ty].x, b = path[tx][ty].y;
                tx = a, ty = b;
                ans.pb({tx,ty});
            }
            int cnt = 1;
            for(int i = ans.size() - 2; i > 0; )
            {
                int tx = ans[i].x, ty = ans[i].y;
                if(d[tx][ty] > 1)
                {
                    cout << cnt << "s:FIGHT AT (" << tx << ',' << ty << ')' << endl;
                    d[tx][ty] --;
                }
                else
                {
                    cout << cnt << "s:(" << ans[i].x << ',' << ans[i].y << ")->(" <<
                    ans[i-1].x << ',' << ans[i-1].y << ')' << endl;
                    i --;
                }
                cnt ++;
            }
            // 特判终点
            while(d[n-1][m-1] > 1)
            {
                cout << cnt << "s:FIGHT AT (" << n-1 << ',' << m-1 << ')' << endl;
                d[n-1][m-1] --;
                cnt ++;
            }
            puts("FINISH");
        }
        else
            cout << "God please help our poor hero." << endl << "FINISH" << endl;
    }

    return 0;
}
```