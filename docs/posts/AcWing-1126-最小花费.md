---
title: AcWing 1126. 最小花费
date: 2022-07-24 15:52:31
tags:
- 最短路
categories: 
- AcWing
- 图论
- 最短路
---

## [AcWing 1126. 最小花费](https://www.acwing.com/problem/content/1128/)
在 n 个人中，某些人的银行账号之间可以互相转账。
这些人之间转账的手续费各不相同。
给定这些人之间转账时需要从转账金额里扣除百分之几的手续费，请问 A 最少需要多少钱使得转账后 B 收到 100 元。

输入格式
第一行输入两个正整数 n,m，分别表示总人数和可以互相转账的人的对数。
以下 m 行每行输入三个正整数 x,y,z，表示标号为 x 的人和标号为 y 的人之间互相转账需要扣除 z% 的手续费 ( z<100 )。
最后一行输入两个正整数 A,B。

数据保证 A 与 B 之间可以直接或间接地转账。

输出格式
输出 A 使得 B 到账 100 元最少需要的总费用。

精确到小数点后 8 位。

数据范围
1≤n≤2000,
m≤105
输入样例：
```
3 3
1 2 1
2 3 2
1 3 3
1 3
```
输出样例：
```
103.07153164
```

### 题解
题目要求的到帐100元的最少费用
100 = x * (1-a1) * (1 - a2) ... x为原来总费用，a1,a2...为手续费
可以简化公式为 100 = x * (p1 * p2 * ... * pn)， 要想求x最小那么就需要（p1 * p2 * ... * pn)最大。
p1 * p2 * ... * pn <=> log(p1 * p2 * ... * pn) <=> log p1 + log p2 + .. log pn
<=> 由于log函数单调递增，所以要想求`p1 * p2 * ... * pn`，那么等于求`p1 + p2 + ... + pn`最大
**但是要注意的是logx 在 (0,1)logx < 0 只会出现负数， 所以有两种做法，要么求最长（边权为负求最长路），要么边权取反求最短路（边权为正求最短路）， 但是一旦x的限制为 x > 0的话，边权就可正可负了，这个情况下就尽量使用spfa**

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
typedef pair<double, int> PDI;
typedef double db;
const ll mod=1000000007;
const int INF = 0x3f3f3f3f;
ll powmod(ll a,ll b) {ll res=1;a%=mod; assert(b>=0); for(;b;b>>=1){if(b&1)res=res*a%mod;a=a*a%mod;}return res;}
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head
const int N = 2010, M = 100010 * 2;

int h[N], e[M], ne[M],idx;
double w[M];
int n, m, start, ed;
double dist[N];
bool st[N];

void add(int a, int b, double c)
{
    e[idx] = b, ne[idx] = h[a], w[idx] = c, h[a] = idx ++;
}

void dijkstra()
{
    memset(dist, -0x3f, sizeof dist);   // 求最长路，初始化为负无穷
    memset(st, 0, sizeof st);

    priority_queue<PDI> q;  // 第一维要保存的是double类型的，且要求最短路这里是大根堆
    q.push({1,start});
    dist[start] = 1;

    while(q.size())
    {
        auto t = q.top();
        q.pop();
        int ver = t.se;
        if(st[ver]) continue;
        st[ver] = true; // 堆优化的dijkstra
        for(int i = h[ver]; ~i; i = ne[i])
        {
            int j = e[i];
            if(dist[j] < dist[ver] * w[i])
            {
                dist[j] = dist[ver] * w[i];
                q.push({dist[j], j});
            }
        }
    }

}

int main()
{
    cin >> n >> m;
    memset(h,-1,sizeof h);
    idx = 0;
    for(int i = 0; i < m; i ++)
    {
        int a, b, c;
        double p;
        scanf("%d%d%d", &a,&b,&c);
        p = (100.0-c) / 100;
        add(a,b,p);
        add(b,a,p);  
    }
    cin >> start >> ed;

    dijkstra();

    printf("%.8lf\n", 100 / dist[ed]);

    return 0;
}
```