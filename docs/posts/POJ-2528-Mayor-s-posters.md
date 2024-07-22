---
title: POJ - 2528 Mayor's posters
date: 2022-07-27 16:15:11
tags:
- 线段树
- 数据结构
categories :
- - POJ
- - 数据结构
---

## [POJ - 2528 Mayor's posters](https://vjudge.net/problem/POJ-2528)
The citizens of Bytetown, AB, could not stand that the candidates in the mayoral election campaign have been placing their electoral posters at all places at their whim. The city council has finally decided to build an electoral wall for placing the posters and introduce the following rules:
Every candidate can place exactly one poster on the wall.
All posters are of the same height equal to the height of the wall; the width of a poster can be any integer number of bytes (byte is the unit of length in Bytetown).
The wall is divided into segments and the width of each segment is one byte.
Each poster must completely cover a contiguous number of wall segments.

They have built a wall 10000000 bytes long (such that there is enough place for all candidates). When the electoral campaign was restarted, the candidates were placing their posters on the wall and their posters differed widely in width. Moreover, the candidates started placing their posters on wall segments already occupied by other posters. Everyone in Bytetown was curious whose posters will be visible (entirely or in part) on the last day before elections.
Your task is to find the number of visible posters when all the posters are placed given the information about posters' size, their place and order of placement on the electoral wall.
Input
The first line of input contains a number c giving the number of cases that follow. The first line of data for a single case contains number 1 <= n <= 10000. The subsequent n lines describe the posters in the order in which they were placed. The i-th line among the n lines contains two integer numbers li and ri which are the number of the wall segment occupied by the left end and the right end of the i-th poster, respectively. We know that for each 1 <= i <= n, 1 <= li <= ri <= 10000000. After the i-th poster is placed, it entirely covers all wall segments numbered li, li+1 ,... , ri.
Output
For each input data set print the number of visible posters after all the posters are placed.

The picture below illustrates the case of the sample input.
![例图](https://vj.csgrandeur.cn/85d8df2191db8df82ba259ebe2bfe93e?v=1658733874)

Sample
Input
```
1
5
1 4
2 6
8 10
3 4
7 10
```
Output
```
4
```

### 题解
题目要我们求出现完整的没有完全被挡住的海报的个数
每次出现一张海报我们都要在该位置坐标上记录其出现次数，用次数来维护没有被完整挡住的海报个数。
分类讨论是否仅使用cnt就能维护
区间覆盖问题
思路一：
如果区间被包含的话，那么如果这个区间先被贴上海报的话，那么这个区间就是被覆盖，这样我们就可以知道，前面的区间是会被后面的区间所影响，但是后面的出现的区间却不会被前面的区间影响。所以在计算答案的时候考虑从后面区间往前面区间开始计算。
思路二：
染色
```C++
struct node
{
    int l, r;
    int co;
    int tag;    // tag = 0表示这个区间没有被染色, tag = 1表示这个区间被染色且这个区间是相同颜色, tag = 2表示这个区间被染色但是染色的颜色不是同一种颜色
}
```




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
const int N = 10010;

struct node
{
    int l, r;
    int co; // 表示在区间单色的情况下被染上的颜色
    // 懒标记
    //int tag; // tag = 0表示没有被染色 1表示区间染色的情况是单色, 2表示的是多色
    int val; // 表示当前区间被染上的颜色
}tr[N * 16];

bool color[N];
vector<int> vx;
PII pos[N];
int n,res;


// void pushup(int u) pushup操作比较麻烦（因为设计到判断这个节点是单色还是多色的问题），所以不如去根节点去找不同的染色情况

int find(int x)
{
    return lower_bound(vx.begin(),vx.end(),x) - vx.begin() + 1;
}

void pushdown(int u)
{
    // 涉及到懒标记下放的时候这时候区间一定是单色情况
    // 假设不是单色情况，但是懒标记没下放。显然是矛盾的，因为要想不是单色其子区间一定有不同颜色，一定会先涉及到区间修改
    if(tr[u].val)
    {
        node &left = tr[u << 1], &right = tr[u << 1 | 1], &root = tr[u];
        left.co = root.val;
        right.co = root.val;
        left.val = root.val;
        right.val = root.val;
        root.val = 0;
    }
}

void build(int u, int l, int r)
{
    if(l == r)
    {
        tr[u].l = l, tr[u].r = r;
        tr[u].co = 0, tr[u].val = 0;
    }
    else
    {
        tr[u].l = l, tr[u].r = r;
        tr[u].co = 0, tr[u].val = 0;
        int mid = l + r >> 1;
        build(u << 1, l, mid), build(u << 1 | 1, mid + 1, r);
    }
}

void modify(int u, int l ,int r, int d)
{
    if(l <= tr[u].l && tr[u].r <= r)
    {
        tr[u].val = d;
        tr[u].co = d;
    }
    else
    {
        pushdown(u);
        int mid = tr[u].l + tr[u].r >> 1;
        if(l <= mid) modify(u << 1, l, r, d);
        if(r > mid) modify(u << 1 | 1, l, r, d);
    }
}

void query(int u, int l, int r)
{
    if(tr[u].l == tr[u].r)
    {
        if(!color[tr[u].co] && tr[u].co)
        {
            color[tr[u].co] = true;
            res ++;
        }
        return; 
    }
    else
    {
        pushdown(u);
        int mid =  tr[u].l + tr[u].r >> 1;
        if(l <= mid) query(u << 1, l, r);
        if(r > mid) query(u << 1 | 1, l, r);
    }
}

int main()
{
    int T;
    cin >> T;
    while(T --)
    {
        cin >> n;
        vx.clear();
        res = 0;
        memset(color,0,sizeof color);
        for(int i = 1; i <= n; i ++)
        {
            int a,b;
            scanf("%d%d",&a,&b);
            pos[i] = mp(a,b);
            vx.pb(a-1),vx.pb(a),vx.pb(b),vx.pb(b+1);
        }

        sort(all(vx));
        vx.erase(unique(all(vx)),vx.end());
        // 本题没有和其他点一样，一个点表示的一个区间，映射下表表示的是单点
        build(1,1,vx.size());
        for(int i = 1; i <= n; i ++)
        {
            modify(1,find(pos[i].fi), find(pos[i].se), i);
        }

        query(1, 1, vx.size());
        printf("%d\n", res);
    }


    return 0;
}
```