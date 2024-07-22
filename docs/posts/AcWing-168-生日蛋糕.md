---
title: AcWing 168. 生日蛋糕
date: 2022-11-15 16:01:36
tags:
- 搜索
- dfs剪枝
categories:
- AcWing
- 搜索
- dfs剪枝
---

# [AcWing 168. 生日蛋糕](https://www.acwing.com/problem/content/170/)

7 月 17 日是 Mr.W 的生日，ACM-THU 为此要制作一个体积为 Nπ 的 M 层生日蛋糕，每层都是一个圆柱体。

设从下往上数第 i 层蛋糕是半径为 Ri，高度为 Hi 的圆柱。

当 i<M 时，要求 Ri>Ri+1 且 Hi>Hi+1。

由于要在蛋糕上抹奶油，为尽可能节约经费，我们希望蛋糕外表面（最下一层的下底面除外）的面积 Q 最小。

令 Q=Sπ ，请编程对给出的 N 和 M，找出蛋糕的制作方案（适当的 Ri 和 Hi 的值），使 S 最小。

除 Q 外，以上所有数据皆为正整数。

输入格式
输入包含两行，第一行为整数 N，表示待制作的蛋糕的体积为 Nπ。

第二行为整数 M，表示蛋糕的层数为 M。

输出格式
输出仅一行，是一个正整数 S（若无解则 S=0）。

数据范围
1≤N≤10000,
1≤M≤20
输入样例：
```
100
2
```
输出样例：
```
68
```

## 题解

**表面积：**

上表面面积：从上往下投影，可以的发现最后的投影面积之和是最底面上表面面积
$$S_底 = R_m^2$$

侧面的面积：
$$\sum_{i=1}^m 2R_ih_i$$


**搜索顺序剪枝**

层间：自下往上

层内：由于R是带平方项的，所以先枚举R再枚举H。都是从大到小开始进行枚举。

**可行性剪枝**

首先做一步定义：

蛋糕的从上面往下层来说依次从`1`开始编号

预处理两个数组`minv(u)`, `maxs(u)`

`minv(u)`: 表示前u层最小体积是多少

* 这里剪枝是 $V_{m \to u} + minv(u) \leq V$

表示的意思是从最下层到当前第u层现在这个方案下的体积V之和，加上上面的u成最小体积之和比我们预定的体积要大的话，表示一定不可行，所以一定要进行剪枝

**同理对于表面积也有同样的剪枝**

`mins(u)`：表示前u层最小表面积是多少

* 这里剪枝是 $S_{m \to u} + mins(u) < ans$

对第u层的半径和高度进行剪枝

**题目上的要求**，对于半径和高度来说，其的数值大小都是需要递增的

**半径**

* 当前层最小值一定是`u`，第一层半径为1，然后依次加最小步长`1`
* 第u层体积的最大值除第u层高度的最小值u

**半径剪枝**

$$u \leq R_u \leq min({ R_{u+1} - 1, \sqrt{ \frac{n - minv(u-1) - V_{m \to u + 1}}{u} } })$$

**高度**

* 当前层最小值一定是`u`，第一层高度为1，然后依次加最小步长`1`
* 第u层体积的最大值除第u层的底面积最小值

**高度剪枝**
$$u \leq H_u \leq min(H_{u+1} - 1, \frac{n - minv(u-1) - V_{m \to u + 1}}{R_u^2})$$

**表面积和体积公式之间的关系**

表面积公式：

$$S_{1 \to u} = 2 \sum_{i=1}^u R_i H_i = \frac{2}{R_{u+1}} \sum_{i=1}^u R_{u+1} R_u H_i > \frac{2}{R_{u+1}} \sum_{i=1}^u R_i^2 H_i$$

体积公式：

$$n - V_{m \to u-1} = \sum_{i=1}^u R_i^2 H_i$$

所以有最后的公式

$$S_{1 \to u} > \frac{2 ( n - V_{m \to u-1}) } {R_{u+1}}$$

$S_总 = S + S_{1 \to u} \geq S_{ans}$
**就可以剪枝了**

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
const int N = 25; // 最多只有20层

int R[N], H[N];
int minv[N], mins[N]; // minv[u] 表示从第一层到第u层（从上往下）的最小体积之和是多少
int n, m, ans = INF; // n 表示体积 m 表示层数

// u表示当前是哪一层
// v表示从最下一层到本层的体积之和
// s表示从最下层到本层的表面积之和
void dfs(int u, int v, int s)
{
    if(v + minv[u] > n) return;
    if(s + mins[u] >= ans) return;
    if(s + 2 * (n-v) / R[u + 1] >= ans) return;

    // 递归出口
    if(!u)
    {
        if(v == n) ans = s;
        return;
    }

    // 枚举本层合法方案
    // 第一层枚半径
    for(int r = min( R[u+1] - 1, (int)sqrt( (n - minv[u-1] - v) / u ) ); r >= u; r --) 
    {
        for(int h = min( H[u+1] - 1, (n - minv[u-1] - v) / r / r ); h >= u; h --)
        {
            int t = 0;
            if(u == m) t = r * r; // 最下层半径决定了整个蛋糕的投影面积
            R[u] = r, H[u] = h;
            dfs(u - 1, v + r * r * h, s + 2 * r * h + t);
        }
    }
}

int main()
{
    cin >> n >> m;
    // 预处理minv & mins数组
    for(int i = 1; i <= m; i ++)
    {
        minv[i] = minv[i-1] + i * i * i;
        mins[i] = mins[i-1] + 2 * i * i; // 这一层高度和半径都最小都是i（步长为1）
    }

    R[m + 1] = H[m + 1] = INF; // 哨兵

    // 优化搜索顺序，从下往上层开始枚举
    dfs(m,0,0);

    if(ans == INF) ans = 0;
    cout << ans << endl;
    return 0;
}
```