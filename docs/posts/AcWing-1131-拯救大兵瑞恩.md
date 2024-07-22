---
title: AcWing 1131. 拯救大兵瑞恩
date: 2022-09-05 14:38:35
tags:
- 图论
- 最短路
categories:
- AcWing
- 图论
- 最短路
- 与dp结合
---

## [AcWing 1131. 拯救大兵瑞恩](https://www.acwing.com/problem/content/1133/)
1944 年，特种兵麦克接到国防部的命令，要求立即赶赴太平洋上的一个孤岛，营救被敌军俘虏的大兵瑞恩。

瑞恩被关押在一个迷宫里，迷宫地形复杂，但幸好麦克得到了迷宫的地形图。

迷宫的外形是一个长方形，其南北方向被划分为 N 行，东西方向被划分为 M 列， 于是整个迷宫被划分为 N×M 个单元。

每一个单元的位置可用一个有序数对 (单元的行号, 单元的列号) 来表示。

南北或东西方向相邻的 2 个单元之间可能互通，也可能有一扇锁着的门，或者是一堵不可逾越的墙。

注意： 门可以从两个方向穿过，即可以看成一条无向边。

迷宫中有一些单元存放着钥匙，同一个单元可能存放 多把钥匙，并且所有的门被分成 P 类，打开同一类的门的钥匙相同，不同类门的钥匙不同。

大兵瑞恩被关押在迷宫的东南角，即 (N,M) 单元里，并已经昏迷。

迷宫只有一个入口，在西北角。

也就是说，麦克可以直接进入 (1,1) 单元。

另外，麦克从一个单元移动到另一个相邻单元的时间为 1，拿取所在单元的钥匙的时间以及用钥匙开门的时间可忽略不计。

试设计一个算法，帮助麦克以最快的方式到达瑞恩所在单元，营救大兵瑞恩。

输入格式
第一行有三个整数,分别表示 N,M,P 的值。

第二行是一个整数 k，表示迷宫中门和墙的总数。

接下来 k 行，每行包含五个整数，Xi1,Yi1,Xi2,Yi2,Gi：当 Gi≥1 时，表示 (Xi1,Yi1) 单元与 (Xi2,Yi2) 单元之间有一扇第 Gi 类的门，当 Gi=0 时，表示 (Xi1,Yi1) 单元与 (Xi2,Yi2) 单元之间有一面不可逾越的墙。

接下来一行，包含一个整数 S，表示迷宫中存放的钥匙的总数。

接下来 S 行，每行包含三个整数 Xi1,Yi1,Qi，表示 (Xi1,Yi1) 单元里存在一个能开启第 Qi 类门的钥匙。

输出格式
输出麦克营救到大兵瑞恩的最短时间。

如果问题无解，则输出 -1。

数据范围
|Xi1−Xi2|+|Yi1−Yi2|=1,
0≤Gi≤P,
1≤Qi≤P,
1≤N,M,P≤10,
1≤k≤150
输入样例：
```
4 4 9
9
1 2 1 3 2
1 2 2 2 0
2 1 2 2 0
2 1 3 1 0 
2 3 3 3 0
2 4 3 4 1
3 2 3 3 0
3 3 4 3 0
4 3 4 4 0
2
2 1 2 
4 2 1
```
输出样例：
```
14
```
样例解释：
迷宫如下所示： ![AcWing](https://cdn.acwing.com/media/article/image/2019/11/06/1_17c87ee400-1131.png)

## 题解
要求从左上脚走到右下角。在途中可能会遇到门，相应的门就需要相应的钥匙，所以途中可能就需要去寻找钥匙，遇到墙就说面不能通过要绕行。
如果对于一个有且仅有墙的问题，我们只需要使用dp[x][y] = min(dp[x + dx][y + dy], dp[x][y])转移,但是的本题因为还需要有寻找钥匙的这个一过成，只有具有一定的状态还可以的通过某一个对应的门，所以在这里需要引入一个状态变量，因为只有十种类型的门，所以最多只需要十位的二进制来表示状态就行。

状态划分
dp[x][y][state] 表示到(x,y)这点状态为state的最小代价 

状态转移
* 当前如果这个位置有钥匙key，因为取钥匙是不需要任何的代价的所以有钥匙就全部取得
 dp[x][y][state | key] = min(dp[x][y][state], dp[x][y][state | key])
* 当前这个点向上下左右四个方向走，可以走的合法方案是（1）没有门和墙 （2）有门且有相应的钥匙
 d[cx][cy][state] = min(d[cx][cy][state], d[x][y][state] + 1)

根据状态转移方程来看我们只有有且仅有边权为0/1， 并没有负数，但有环。所以一定是没有的负环，一定是可以通过最短路来求解不能通过dp来求解.

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
const int N = 105, M = 400, STA = 1 << 10;

int h[N], e[M], ne[M], w[M], idx;
int g[N][N], key[N * N];
int dist[N][STA], dx[4] = {0, 0, 1, -1}, dy[4] = {1, -1, 0, 0};
bool st[N][STA];
int n, m, P, K;
set<PII> edges;
deque<PII> q;

void add(int a,int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx ++;
}

void build()
{   
    // 步骤为：枚举每一个点，然后看他的邻接点是否建立边
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= m; j ++)
        {
            if(!i || i > n || !j || j > m) continue;

            for(int k = 0; k < 4; k ++)
            {
                int x = i + dx[k], y = j + dy[k];
                int a = g[i][j], b = g[x][y];
                if(!edges.count({a,b})) add(a,b,0);
            }
        }
}

int bfs()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1][0] = 0;
    q.pb({1,0});

    while(q.size())
    {
        auto t = q.front();
        q.pop_front();

        int ver = t.fi, sta = t.se;
        if(st[ver][sta]) continue;   //  每个点只会被更新一次
        st[ver][sta] = true;

        // 剪枝
        // 每个点的每一种状态只会被更新一遍
        if(ver == n * m) return dist[ver][sta];

        // 判断该点上是否有钥匙 
        if(key[ver])
        {
           int state = sta | key[ver];
           if(dist[ver][state] > dist[ver][sta])
            {
                dist[ver][state] = dist[ver][sta];
                q.push_front({ver,state});
            }
        }

        // 这个点放邻边走
        for(int i = h[ver]; ~i; i = ne[i])
        {
            int j = e[i];   // 邻边的编号（该情况一定没有墙，因为有墙的话就不会有边相连，所以这里只用简化到判断是否有门）

            // 当前没有门
            if((!w[i] || sta >> w[i] - 1 & 1) && dist[j][sta] > dist[ver][sta] + 1)
            {
                dist[j][sta] = min(dist[j][sta], dist[ver][sta] + 1);
                q.push_back({j,sta});
            }   
        }
    }

    return -1;
}

int main()
{
    cin >> n >> m >> P >> K;

    // 为了将二维转换成一维的关系，将二维坐标转换成一维的编号方式
    for(int i = 1, t = 1; i <= n; i ++)
        for(int j = 1; j <= m; j ++)
            g[i][j] = t ++;

    // 建立墙和门
    memset(h, -1, sizeof h);
    for(int i = 0; i < K; i ++)
    {
        int x1,y1,x2,y2,c;
        cin >> x1 >> y1 >> x2 >> y2 >> c;
        int a = g[x1][y1], b = g[x2][y2];
        edges.insert({a,b}), edges.insert({b,a});   // 这里抱存进set的原因是因为方便后面的建图，判断当前的边是否已经被建立
        // 如果是墙的话就说明没有边（等价于无路可走）
        if(c) add(a,b,c),add(b,a,c); // 在编号为a和b之间建立一条边，边权为c的边
    }

    // 建立普通边
    build(); 

    // 记录每个坐标上的key值
    int s;
    cin >> s;
    for(int i = 0; i < s; i ++)
    {
        int x,y,c;
        cin >> x >> y >> c;
        int a = g[x][y];
        key[a] |= 1 << c - 1;
    }

    // 双端队列求解最短路 
    cout << bfs() << endl;
    
    return 0;
}
```