---
title: AcWing 340. 通信线路
date: 2022-09-01 14:59:05
tags:
- 图论
- 最短路
categories:
- AcWing
- 图论
- 最短路
- 与二分结合
---

## [AcWing 340. 通信线路](https://www.acwing.com/problem/content/342/)
在郊区有 N 座通信基站，P 条 双向 电缆，第 i 条电缆连接基站 Ai 和 Bi。

特别地，1 号基站是通信公司的总站，N 号基站位于一座农场中。

现在，农场主希望对通信线路进行升级，其中升级第 i 条电缆需要花费 Li。

电话公司正在举行优惠活动。

农产主可以指定一条从 1 号基站到 N 号基站的路径，并指定路径上不超过 K 条电缆，由电话公司免费提供升级服务。

农场主只需要支付在该路径上剩余的电缆中，升级价格最贵的那条电缆的花费即可。

求至少用多少钱可以完成升级。

输入格式
第 1 行：三个整数 N，P，K。

第 2..P+1 行：第 i+1 行包含三个整数 Ai,Bi,Li。

输出格式
包含一个整数表示最少花费。

若 1 号基站与 N 号基站之间不存在路径，则输出 −1。

数据范围
0 ≤ K < N ≤ 1000
1 ≤ P ≤ 10000,
1 ≤ Li ≤ 1000000
输入样例：
```
5 7 1
1 2 5
3 1 4
2 4 8
3 2 3
5 2 9
3 4 7
4 5 6
```
输出样例：
```
4
```

### 题解
根据题意：我们知道在这个图上的某两个点之间的所有路径去除K条最贵的路径，剩下的所有边权最大值最小。
* 与最小生成树做对比：这里是路径，是没有必要经过所有的点，所以如果使用kruscal可能求出来的解不对
* 与最短路径做对比：在极端的情况下最短路径的次大值可能比更长的路径的最大值要大，所以要是求出最短路径然后再求出次大值这样求出来的答案也不一定是正确的

不严谨来讲，本题也就是要求第`k+1`大的边最小(最大值最小 --> 二分思想)
但路径上的边数小于`K`条,那么说明所有的边都被干掉，最优解为`0`

要使用二分就一定要具有二段性的性质（也就是说答案的左边是一个性质，答案的右边是另外一个性质）
假设答案定义的在[0,1000001]区间上
对于区间中的某一个点`x`，求出从`1`走到`N`中，最少经过几条长度大于`x`的边，假设最少经过`y`条。
性质的结果就是`y <= k`。

证明这个性质成立
* x右区间所有点满足性质
 所有的路径中的最少经过的长度大于`x`的边为`y`条，并且在当前区间为`x`点下, `y <= k`，也就是说答案在比x大的情况下一定成立，那么x的右边区域就满足性质。
* x的左区间所有点都不满足性质
 反证法：假设存在一个点满足性质，那么这个点一定是我们答案x'点（因为答案右区间一定满足性质，如果当前的点右区间满足性质，那么答案一定会被更新到这个点上），这样就和我们前提答案为x点矛盾。

区间取到`0` 与 `1000001`
* 取到0时，路径上的边数小于`K`，这样子答案就为0
* 取到1000001时，由于本题是又可能`1->N`之间不存在路径的，所以是有可能存在无解的情况，如果区间的不取到`1000001`，而是取到的`1000000`时，有可能最大值和无解都重合在`1000000`这个点上具有二义性，所以为了区分情况，多设置了一个点表示无解的情况.

求出从1到N最少经过几条长度大于x的边 
可以将所有的边进行分类，当前边权大于x的边，边权重新设置为1；当前边权小于等于x的边设为0.
这样的话，一个图上只有两种边权不是0/1.我们就可以使用双端队列bfs来求出最短路（最短路也就是0越少越好，最短路边权等于大于x的边的数目）

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

const int N = 1010, M = 20010; 

int h[N], e[M], ne[M], w[M], idx;
int dist[N];
int n, m, k;
deque<int> q;   // save 0/1
bool st[N];

void add(int a, int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx ++;
}

bool check(int bound)
{
    // deque find the shortest path

    // init
    memset(st, 0, sizeof st); // means all point not update
    memset(dist, 0x3f, sizeof dist);
    q.push_back(1);
    dist[1] = 0;

    // like dijstra
    while(q.size())
    {
        int t = q.front();
        q.pop_front();

        if(st[t]) continue;
        st[t] = true;

        // update dist 
        for(int i = h[t]; ~i; i = ne[i])
        {
            int j = e[i], v = w[i] > bound; // value > bound set '1' or set '0'
            if(dist[j] > dist[t] + v) 
            {
                dist[j] = dist[t] + v;
                if(!v) q.push_front(j);
                else q.push_back(j);
            }    
        }   
    }
    // 这里的最短路边权值等于大于bound的边数，如果边数比k还要小的话，那么答案应该
    return dist[n] <= k;
}


int main()
{
    // read
    cin >> n >> m >> k;

    // build
    memset(h, -1, sizeof h);
    for(int i = 0; i < m; i ++)
    {
        int a, b, c;
        cin >> a >> b >> c;
        add(a, b, c), add(b, a, c);
    }

    // binary_search
    int l = 0, r = 1000001; // [0,1e6 + 1]
    // find the minimum of all maximums
    while(l < r)
    {
        int mid = l + r >> 1;
        // 检查当前的点是否满足性质 y <= k
        if(check(mid)) r = mid; //  if statisfy find the lefe interval 
        else l = mid + 1;
    }

    if(r == 1e6 + 1) cout << -1 << endl;
    else cout << r << endl;
    return 0;
}
```