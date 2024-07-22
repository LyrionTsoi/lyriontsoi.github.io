---
title: CodeForces - 777E Hanoi Factory
date: 2022-07-31 16:16:48
tags:
- 动态规划
- 线段树
categories: 
- CodeForces
- 动态规划
- 线段树优化
---

## [CodeForces - 777E Hanoi Factory](https://vjudge.net/problem/CodeForces-777E#author=0)

Of course you have heard the famous task about Hanoi Towers, but did you know that there is a special factory producing the rings for this wonderful game? Once upon a time, the ruler of the ancient Egypt ordered the workers of Hanoi Factory to create as high tower as possible. They were not ready to serve such a strange order so they had to create this new tower using already produced rings.

There are n rings in factory's stock. The i-th ring has inner radius ai, outer radius bi and height hi. The goal is to select some subset of rings and arrange them such that the following conditions are satisfied:

Outer radiuses form a non-increasing sequence, i.e. one can put the j-th ring on the i-th ring only if bj ≤ bi.
Rings should not fall one into the the other. That means one can place ring j on the ring i only if bj > ai.
The total height of all rings used should be maximum possible.
Input
The first line of the input contains a single integer n (1 ≤ n ≤ 100 000) — the number of rings in factory's stock.

The i-th of the next n lines contains three integers ai, bi and hi (1 ≤ ai, bi, hi ≤ 109, bi > ai) — inner radius, outer radius and the height of the i-th ring respectively.

Output
Print one integer — the maximum height of the tower that can be obtained.

Sample 1
Input
```
3
1 5 1
2 6 2
3 7 3
```
Output
```
6
```
Sample 2
Input
```
4
1 2 1
1 3 3
4 6 2
5 7 1
```
Output
```
4
```
Note
In the first sample, the optimal solution is to take all the rings and put them on each other in order 3, 2, 1.

In the second sample, one can put the ring 3 on the ring 4 and get the tower of height 3, or put the ring 1 on the ring 2 and get the tower of height 4.

### 题解
dp[i] ：以i结尾的所有合法方案的最大值
ans = max ∑ dp[i]

合法方案
$ai < bj <= bi$ 

正常的dp
Step1 ：对bi进行从大到小进行排序
Step2 : 从前bi个区间中找出所有方案的中的最大价值
Step3 ：更新dp[bi]
时间复杂度：$O(n^2)$

树状数组优化：
主要对step2进行优化的对$O(n)$优化成$O(logn)$

合法方案是包括两个决策变量的
1. ai < bj
2. bj <= bi
通过对bi从大到小排序再去枚举的话我们就可以减少一个决策，然后离散a数组，这样就可以构造出来一个单调的a数组，用线段树进行维护我们就可以的用$O(nlogn)$来解决问题


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
const int N = 100010;

struct node
{
    int a,b,h;
    bool operator <(const node & t)const
    {
        if(b == t.b) return a > t.a;
        return b > t.b;
    }
}ring[N];

struct Node
{
    int l, r;
    ll maxn;
}tr[N << 2];


int n;
ll dp[N];
vector<int> va;

int find(ll x)
{
    return lower_bound(va.begin(), va.end(), x) - va.begin() + 1;
}

void pushup(int u)
{
    tr[u].maxn = max(tr[u << 1].maxn, tr[u << 1 | 1].maxn);
}

void build(int u, int l, int r)
{
    if(l == r)
    {
        tr[u] = {l,r,0};
    }
    else
    {
        tr[u] = {l,r,0};
        int mid = l + r >> 1;
        build(u << 1, l, mid), build(u << 1 | 1, mid + 1, r);
    }
}

void modify(int u, int x, ll d)
{
    if(tr[u].l == x && tr[u].r == x)
    {
        tr[u].maxn = max(tr[u].maxn, d);
    }
    else
    {
        int mid = tr[u].l + tr[u].r >> 1;
        if(x <= mid) modify(u << 1, x, d);
        if(x > mid) modify(u << 1 | 1, x, d);
        pushup(u);
    }
}

ll query(int u, int l, int r)
{
    if(l <= tr[u].l && tr[u].r <= r)
    {
        return tr[u].maxn;
    }
    else
    {
        int mid = tr[u].l + tr[u].r >> 1;
        ll res = 0;
        if(l <= mid) res = query(u << 1, l, r);
        if(r > mid) res = max(res, query(u << 1 | 1, l, r));
        return res;
    }
}

int main()
{
    cin >> n;

    for(int i = 1; i <= n; i ++)
    {
        int a,b,h;
        cin >> a >> b >> h;
        ring[i] = {a,b,h};
        va.pb(a);
    }

    // 动态规划的策略选择是ai < bj <= bi
    // 所以如果先对bi进行排序这样找到选择方案的时候我们不需要判断bj <= bi，因为枚举时候的bj总是的比bi晚枚举
    sort(ring + 1, ring + n + 1);
    sort(all(va));
    va.erase(unique(all(va)),va.end());
    build(1,1,n);

    ll ans = 0;
    for(int i = 1; i <= n; i ++)
    {
        // 因为已经按照b从大向小排序，所以需要二分找到的是ai < bj
        dp[i] = query(1,1,find(ring[i].b) - 1) + ring[i].h;
        // 因为我们是离散构造出来va数组，并利用va数组的单调性二分查找ai < bj所以要在ring[i].a上修改值
        modify(1, find(ring[i].a), dp[i]);
        ans = max(ans, dp[i]);
    }

    printf("%lld\n",ans);

    return 0;
}
```
