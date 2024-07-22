---
title: AcWing 1077. 皇宫看守
date: 2022-08-07 17:48:51
tags:
- 树形dp
categories:
- AcWing
- 动态规划
- 树形dp
---

### [AcWing 1077. 皇宫看守](https://www.acwing.com/problem/content/1079/)
本题要区别于[战略游戏](https://www.acwing.com/problem/content/325/)
战略游戏是要求我们的用最少节点数覆盖到所有点上
本题是要求我们用最少的节点数覆盖到所有的边上

比如一个图是
1->2->3->4
最少点覆盖是用1号点和4号点这样就可以覆盖全图上的所有点（方案不唯一)
最少边覆盖是用1，2，3号点才可以实现所有的边覆盖

树形dp
状态划分dp[i][3]
要注意的是是以i为根的子树也就是只能当前节点的往下去划分集合
集合划分：dp[i][0] 表示以i为根的子树父节点被选自己观察的情况， dp[i][1] 表示以i为根的子树父节点被子节点观察的情况,dp[i][2] 表示以i为根的子树父节点被其父节点观察
状态转移：
当父节点被自己观察的情况下，子节点要么被自己观察要么被被自己的儿子观察
dp[u][0] = min(dp[v][0], dp[v][1], dp[v][2]) + w[u];
当父亲节点被子节点观察的情况，至少一个子节点是被自己观察，其余的节点可以被自己观察也可以被子节点观察
dp[u][1] = min(dp[v][0] + ∑ min(dp[vi][0],dp[vi][1]))
当父亲节点被父节点观察的情况，那么其子节点是可以被自己观察也可以被子节点观察的
dp[u][2] = min(dp[v][0],dp[v][1])

###CODE
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
const int N = 1510, M = 2 * N;

int h[N], e[M], ne[M], w[N], idx;
int dp[N][3];
int n,res = INF;

void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

void dfs(int u, int fa)
{
    if(u == -1) return;

    dp[u][0] = w[u];    // 被自己观察那么至少是选择当前的节点的
    dp[u][1] = INF;     // 被儿子观察，因为要遍历所子节点方案取min所以要初始化为INF
    dp[u][2] = 0;       // 被父节点观察的情况下，当下这个节点一定不用选
    for(int i = h[u]; ~i; i = ne[i])
    {
        int j = e[i];
        if(j == fa) continue;
        dfs(j,u);
        // cout << "A" << endl;
        dp[u][0] += min(dp[j][0], min(dp[j][1], dp[j][2]));
        dp[u][2] += min(dp[j][0],dp[j][1]);
    }

    for(int i = h[u]; ~i; i = ne[i])
    {
        int v = e[i];
        if(v == fa) continue;
        // cout << "B" << endl;
        // dp[u][2] = sum{min(dp[v][0],dp[v][1])}
        // dp[u][2] - j的贡献就等于其他子节点的贡献总和
        dp[u][1] = min(dp[u][1], dp[v][0] + dp[u][2] - min(dp[v][0],dp[v][1]));
    }
}

int main()
{
    cin >> n;

    memset(h,-1,sizeof h);
    while(n --)
    {
        int id,v,cnt;
        cin >> id >> v >> cnt;
        w[id] = v;
        while(cnt --)
        {
            int a;
            cin >> a;
            add(id,a), add(a,id);
        }
    }

    dfs(1,-1);

    cout << min(dp[1][0], dp[1][1]) << endl;

    return 0;
}
```