---
title: AcWing 341 最优贸易
date: 2022-09-03 16:07:03
tags:
- 图论
- 最短路
categories:
- AcWing
- 图论
- 最短路
- 与dp结合
---

## [AcWing 341 最优贸易](https://www.acwing.com/problem/content/343/)
C 国有 n 个大城市和 m 条道路，每条道路连接这 n 个城市中的某两个城市。

任意两个城市之间最多只有一条道路直接相连。

这 m 条道路中有一部分为单向通行的道路，一部分为双向通行的道路，双向通行的道路在统计条数时也计为 1 条。

C 国幅员辽阔，各地的资源分布情况各不相同，这就导致了同一种商品在不同城市的价格不一定相同。

但是，同一种商品在同一个城市的买入价和卖出价始终是相同的。

商人阿龙来到 C 国旅游。

当他得知“同一种商品在不同城市的价格可能会不同”这一信息之后，便决定在旅游的同时，利用商品在不同城市中的差价赚一点旅费。

设 C 国 n 个城市的标号从 1∼n，阿龙决定从 1 号城市出发，并最终在 n 号城市结束自己的旅行。

在旅游的过程中，任何城市可以被重复经过多次，但不要求经过所有 n 个城市。

阿龙通过这样的贸易方式赚取旅费：他会选择一个经过的城市买入他最喜欢的商品——水晶球，并在之后经过的另一个城市卖出这个水晶球，用赚取的差价当做旅费。

因为阿龙主要是来 C 国旅游，他决定这个贸易只进行最多一次，当然，在赚不到差价的情况下他就无需进行贸易。

现在给出 n 个城市的水晶球价格，m 条道路的信息（每条道路所连接的两个城市的编号以及该条道路的通行情况）。

请你告诉阿龙，他最多能赚取多少旅费。

注意：本题数据有加强。

输入格式
第一行包含 2 个正整数 n 和 m，中间用一个空格隔开，分别表示城市的数目和道路的数目。

第二行 n 个正整数，每两个整数之间用一个空格隔开，按标号顺序分别表示这 n 个城市的商品价格。

接下来 m 行，每行有 3 个正整数，x，y，z，每两个整数之间用一个空格隔开。

如果 z=1，表示这条道路是城市 x 到城市 y 之间的单向道路；如果 z=2，表示这条道路为城市 x 和城市 y 之间的双向道路。

输出格式
一个整数，表示答案。

数据范围
1≤n≤100000,
1≤m≤500000,
1≤各城市水晶球价格≤100
输入样例：
```
5 5
4 3 5 6 1
1 2 1
1 4 1
2 3 2
3 5 1
4 5 2
```
输出样例：
```
5
```

## 题解

### 要知道dp是如何和最短路结合的并且是可以结合条件是什么？？
对于一个具有依赖关系的dp问题
 比如 
 dp[2] = min(dp[1] + 1,dp[4] + 1)
 dp[3] = dp[2] + 1
 dp[4] = dp[3] + 1
 dp[5] = dp[4] + 1
画出状态转移图，这样就知道了这是一个带环的但是知道每条边的边权都是为正，但是图上没有带负数环所以是可以用最短路来求的。

**总结：** 能用最短路来求dp问题的前提条件是的状态转移图不存在负环，且在这个图上由于具有环结构所以是不能用dp的状态转移来求

本题的状态转移和状态划分
* 对于求方案数类型的dp问题要求状态划分做到不重不漏
* 对于求最优解类型的dp问题最低要求状态划分做到不漏（可以重复）

所以对于本题的状态划分为对每个节点的来说在这个节点之前购买的最低价格和在这个节点之后卖出最高价格
最后的答案就等于ans = dpmax[i] - dpmin[i]

**本题要注意的是，只有点权没有边权，并且点权是不能放在边权上面的。**
dijkstra实际上就是利用了贪心的思想具有局部最优解—>全局最优解，并且每个点只会被更新一次
 如果当前` dmin[i]` 最小的点是` 5，`那么有可能存在边 5-> 6, 6-> 7, 7-> 5，假设当前` dmin[5] = 10`，则有可能存在` 6 `的价格是11， 但 `7` 的价格是3，那么` dmin[5] `的值就应该被更新成3，因此当前最小值也不一定是最终最小值，所以dijkstra算法并不适用，我们只能采用 spfa 算法。

`SPFA`算法最坏的时间复杂度为nm = 100000 * 5000000 ,但是不卡数据的情况下时间复杂度大概在O(m)

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
const int N = 100010, M = 500000 * 2;

int zh[N], rh[N], w[N], e[M], ne[M], idx; 
int dpmin[N],dpmax[N];
int q[N];
bool st[N];
int n, m;

void add(int h[],int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

void spfa(int dp[], int S, int h[])
{
    // init
    int hh = 0, tt = 1;

    if(S == 1)
        memset(dp, 0x3f, sizeof dpmin);// 要求最短路
    else memset(dp, -0x3f, sizeof dpmax);

    st[S] = true;
    dp[S] =  w[S];
    q[hh] = S;
    
    while(hh != tt)
    {
        int t = q[hh ++];
        if(hh == N) hh = 0; // 循环队列 
        st[t] = false;

        for(int i = h[t]; ~i; i = ne[i])
        {
            int j = e[i];

            if(S == 1 && dp[j] > min(dp[t], w[j]) || S == n && dp[j] < max(dp[t], w[j]) )
            {
                if(S == 1) dp[j] = min(dp[t], w[j]);
                else dp[j] = max(dp[t],w[j]);

                if(!st[j])
                {
                    q[tt ++] = j;
                    if(tt == N) tt = 0;
                    st[j] = true;
                }
            }
        }
    }
}

int main()
{
    cin >> n >> m;

    for(int i = 1; i <= n; i ++)
        cin >> w[i];

    memset(zh, -1, sizeof zh);
    memset(rh, -1, sizeof rh);
    for(int i = 0; i < m; i ++)
    {
        int a, b, c;
        scanf("%d%d%d",&a,&b,&c);
        add(zh, a, b), add(rh, b, a);
        if(c == 2)
            add(zh, b, a), add(rh, a, b);
    }

    spfa(dpmin, 1, zh);
    spfa(dpmax, n, rh);

    int ans = -INF;
    for(int i = 1; i <= n; i ++)
        ans = max(ans, dpmax[i] - dpmin[i]);

    cout << ans << endl;
    
    return 0;
}
```