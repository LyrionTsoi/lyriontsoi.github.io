---
title: POJ - 1947 Rebuilding Roads
date: 2022-08-06 14:25:05
tags:
- 动态规划
- 树形dp
categories:
- POJ
- 动态规划
- 树形dp
- 树上背包
---

## [POJ - 1947 Rebuilding Roads](https://vjudge.net/problem/POJ-1947#author=0)
The cows have reconstructed Farmer John's farm, with its N barns (1 <= N <= 150, number 1..N) after the terrible earthquake last May. The cows didn't have time to rebuild any extra roads, so now there is exactly one way to get from any given barn to any other barn. Thus, the farm transportation system can be represented as a tree.

Farmer John wants to know how much damage another earthquake could do. He wants to know the minimum number of roads whose destruction would isolate a subtree of exactly P (1 <= P <= N) barns from the rest of the barns.

**Input**
* Line 1: Two integers, N and P

* Lines 2..N: N-1 lines, each with two integers I and J. Node I is node J's parent in the tree of roads.

**Output**
A single line containing the integer that is the minimum number of roads that need to be destroyed for a subtree of P nodes to be isolated.

Sample
Input
```
11 6
1 2
1 3
1 4
1 5
2 6
2 7
2 8
4 9
4 10
4 11
```

Output
```
2
```

### 题解
大意：一颗n个节点树要我们剪最少的边最后得到一颗m个节点的树，求剪的最少的边数

树形dp
重要的是如何对集合的划分
在这里就相当一颗以u为根节点的数的，以他的孩子为根就相当于每一组分组，从每一个种选择某一种方案组成的最优方案
属性：剪的最少边数
集合：dp[i][j] 以i位根的树保留j个节点需要的剪的最少边数
状态转移：dp[u][j] = min(dp[u][j],dp[u][j - k] + dp[v][k]);

但本题有几个细节
1. 最优解不一定是通过的根节点决定
2. 树上背包要是通过子节点来更新，那么一定要保留父节点到子节点之间的边

### CODE
```C++
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
const int N = 155, M = N;

int h[N], e[M], ne[M], idx;
int n, m;
int son[N],dp[N][N];    // son[i]表示一个节点的子节点有多少个
bool st[N];

void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

// 返回的是以u为根节点的树的节点总数
int dfs(int u)
{
    if(u == -1) return 0;

    // 如果只保留一个节点的话那么就把以u为根的节点到其所有的子节点的边都删掉
    dp[u][1] = son[u];
    int sum = 1;
    for(int i = h[u]; ~i; i = ne[i])
    {
        int v = e[i];
        sum += dfs(v);
        for(int j = sum; j >= 0; j --)  
            for(int k = 1; k <= j; k ++)    // 表子树保留k个节点的
                dp[u][j] = min(dp[u][j],dp[v][k] + dp[u][j-k] - 1); // 要是用v来更新父节点的话，u->v之间的边是不能删去的
    }
    return sum;
}

int main()
{
    cin >> n >> m;  

    memset(h,-1,sizeof h);
    memset(dp,0x3f,sizeof dp);
    for(int i = 0; i < n - 1; i ++)
    {
        int a, b;
        cin >> a >> b;
        add(a,b);
        son[a] ++;
        st[b] = true;
    }

    int root = 1;
    while(st[root]) root ++;

    dfs(root);

    int res = dp[root][m];

    // 可能在子树上(也就是如果我们剪一刀就剪出来一颗子树为m个节点那么很显然答案就是“1”)
    for(int i = 1; i <= n; i ++)
        if(dp[i][m] < res)
            res = dp[i][m] + 1; // 要加一因为一刀就切出来m个节点的树那么这一刀是切在这颗子树的根节点上面

    cout << res << endl;
    return 0;
}
```