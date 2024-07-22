---
title: AcWing 1074. 二叉苹果树
date: 2022-08-07 15:53:30
tags:
- 树形dp
categories:
- AcWing
- 动态规划
- 树形dp
- 树上背包
---

## [AcWing 1074. 二叉苹果树](https://www.acwing.com/problem/content/1076/)
有一棵二叉苹果树，如果树枝有分叉，一定是分两叉，即没有只有一个儿子的节点。

这棵树共 N 个节点，编号为 1 至 N，树根编号一定为 1。

我们用一根树枝两端连接的节点编号描述一根树枝的位置。

一棵苹果树的树枝太多了，需要剪枝。但是一些树枝上长有苹果，给定需要保留的树枝数量，求最多能留住多少苹果。

这里的保留是指最终与1号点连通。

输入格式
第一行包含两个整数 N 和 Q，分别表示树的节点数以及要保留的树枝数量。

接下来 N−1 行描述树枝信息，每行三个整数，前两个是它连接的节点的编号，第三个数是这根树枝上苹果数量。

输出格式
输出仅一行，表示最多能留住的苹果的数量。

数据范围
1≤Q<N≤100.
N≠1,
每根树枝上苹果不超过 30000 个。

输入样例：
```
5 2
1 3 1
1 4 10
2 3 20
3 5 20
```
输出样例：
```
21
```

###  题解
根据题意我们要知道的是对于本题来说选了子节点，其子节点的父亲节点也一定要选，但是选了父节点不一定选子节点，所以知道了从下到上更新困难，从上到下更新简单。

有依赖关系的背包问题划分：
dp[i][j]表示以i为根的子树，包含i的这个节点保留边数不大于j的权值之和最大方案

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
const int N = 110, M = N * 2;

int h[N], e[M], ne[M], w[M], idx;
int dp[N][N];   // dp[u][m]表示以u为根节点的树保留m条边最多可以留的苹果数目
bool st[N];
int n,m;

void add(int a, int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx ++;
}

void dfs(int u, int fa)
{
    if(u == -1) return;

    for(int i = h[u]; ~i; i = ne[i])
    {
        int v = e[i];
        if(v == fa) continue;

        dfs(v,u);

        for(int j = m; j >= 0; j --)
            for(int k = 0; k < j; k ++)
                dp[u][j] = max(dp[u][j], dp[v][k] + dp[u][j-k-1] + w[i]);   // 要保留u->v之间的那条边
    }   
}

int main()
{
    cin >> n >> m;

    memset(h,-1,sizeof h);
    for(int i = 0; i < n-1; i ++)
    {
        int a, b, c;
        scanf("%d%d%d",&a,&b,&c);
        // 不能建立单向边
        /*
            1 2 0
            1 5 1
            3 5 100
            4 5 1
        */
        add(a,b,c),add(b,a,c);
    }

    dfs(1,-1);

    cout << dp[1][m] << endl;
    
    return 0;
}
```