---
title: CodeForces - 1529C Parsa's Humongous Tree
date: 2022-08-05 21:10:40
tags:
- 动态规划
- 树形dp
- 贪心
categories:
- CodeForces
- 动态规划
- 树形dp
---

## [CodeForces - 1529C Parsa's Humongous Tree](https://vjudge.net/problem/CodeForces-1529C)
Parsa has a humongous tree on 𝑛 vertices.

On each vertex 𝑣 he has written two integers 𝑙𝑣 and 𝑟𝑣.

To make Parsa's tree look even more majestic, Nima wants to assign a number 𝑎𝑣 (𝑙𝑣≤𝑎𝑣≤𝑟𝑣) to each vertex 𝑣 such that the beauty of Parsa's tree is maximized.

Nima's sense of the beauty is rather bizarre. He defines the beauty of the tree as the sum of |𝑎𝑢−𝑎𝑣| over all edges (𝑢,𝑣) of the tree.

Since Parsa's tree is too large, Nima can't maximize its beauty on his own. Your task is to find the maximum possible beauty for Parsa's tree.

Input
The first line contains an integer 𝑡 (1≤𝑡≤250) — the number of test cases. The description of the test cases follows.

The first line of each test case contains a single integer 𝑛 (2≤𝑛≤105) — the number of vertices in Parsa's tree.

The 𝑖-th of the following 𝑛 lines contains two integers 𝑙𝑖 and 𝑟𝑖 (1≤𝑙𝑖≤𝑟𝑖≤109).

Each of the next 𝑛−1 lines contains two integers 𝑢 and 𝑣 (1≤𝑢,𝑣≤𝑛,𝑢≠𝑣) meaning that there is an edge between the vertices 𝑢 and 𝑣 in Parsa's tree.

It is guaranteed that the given graph is a tree.

It is guaranteed that the sum of 𝑛 over all test cases doesn't exceed 2⋅105.

Output
For each test case print the maximum possible beauty for Parsa's tree.

**Example**
Input
```
3
2
1 6
3 8
1 2
3
1 3
4 6
7 9
1 2
2 3
6
3 14
12 20
12 19
2 12
10 17
3 17
3 2
6 5
1 5
2 6
4 6
```
Output
```
7
8
62
```

### 题解
题目要求每个节点在区间[l,r]中选一个值，每一条边的边权 == 两点的之间的绝对值，求出一颗树中的边权的最大值

**贪心**
贪心决策： 实际上每个树的节点只取选取l/r

贪心证明
前提父节点为u，父节点有多个子节点，父节点的数值为au,子节点为数值为av，有p个比au大的子节点单av， 有q个比au小的子节点
 1. p > q   ∑ |au - av| 那么au 应该越小求和越大(比如在[3,8]区间中选一个中值，x 假设有两个av ==  9， 一个 av = 2，当x取5的时候∑ = 3 + 4 + 4，但是当x取3的时候 ∑ = 1 + 6 + 6,因为比贡献等等于p的个数累加的)
 2. p < q 
 3. p = q
   
所以我们就可以定义dp数组
dp[i][0]表示选的l,dp[i][1]选r

因为l、r< 1e9 ，所以总和是可能爆int，记得用long long

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
#define int long long 
typedef vector<int> VI;
typedef basic_string<int> BI;
typedef long long ll;
typedef pair<int,int> PII;
typedef double db;
const ll mod=1000000007;
const int INF = 0x3f3f3f3f;
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head
const int N = 100010, M = 2 * N;

int dp[N][2];   // dp[i][0]表示选的l,dp[i][1]选r
int n;
int h[N],e[M],ne[M],idx;    // tree(无向边)
PII node[N];
bool st[N];

void add(int a,int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

void dfs(int u,int fa)
{
    if(u == -1) return;
    for(int i = h[u]; ~i; i = ne[i])
    {
        int j = e[i];
        if(j == fa) continue;
        dfs(j,u);
        dp[u][0] += max(dp[j][0] + abs(node[u].fi - node[j].fi), dp[j][1] + abs(node[u].fi - node[j].se));
        dp[u][1] += max(dp[j][0] + abs(node[u].se - node[j].fi), dp[j][1] + abs(node[u].se - node[j].se));
    }
}


signed main()
{
    int T;
    cin >> T;
    while(T --)
    {
        // init()
        memset(dp,0,sizeof dp);
        memset(st,false, sizeof st);
        memset(h,-1,sizeof h);
        idx = 0;

        cin >> n;
        for(int i = 1; i <= n; i ++)
        {
            int l, r;
            scanf("%d%d",&l,&r);
            node[i] = {l,r};
        }

        for(int i = 0; i <  n - 1; i ++)
        {
            int a,b;
            scanf("%d%d",&a,&b);
            add(a,b);
            add(b,a);
        }

        dfs(1,-1);
        cout << max(dp[1][0],dp[1][1]) << endl;
    }

    return 0;
}
```

