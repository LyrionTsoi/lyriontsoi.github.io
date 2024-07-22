---
title: Floyd总结
date: 2022-11-20 16:15:58
tags:
- 总结
- Floyd
categories:
- AcWing
- 图论
- Floyd
---

# Floyd总结

## Floyd用法

Floyd有以下的几种用法：

1. 最短路
2. 传递闭包
3. 找最小环
4. 恰好进过`k`条边的最短路（利用倍增思想）

## Floyd证明

**证明Floyd原理**

Floyd是基于**动态规划**原理

**状态表示** ,d[k,i,j]
集合： 所有从i出发，最终走到j，且中间只经过节点编号不超过k的所有路径
属性： 路径长度的最小值

**状态计算**
这里的状态划分可以分成`所有不含节点k的路径` 和 `所有包含节点k的路径`
状态转移
* 如果不包含节点`k`就意味着从编号$1 \to {k-1}$编号中组成的i到j的最短路径。
* 如果包含节点`k`就意味着这个最短路径的过程是从`i`走到`k`，然后再从`k`走到`j`。又因为这条i~j的这条路径是由编号为前k号的路径组成的，所以这里的两条路径(两条路径都是独立的，所以两边的都取最小值），可以这样定义。`dist[k-1][i][k] + dist[k-1][k][j] `

**这解释一下状态转移第二条，两条路径独立的原因**，我们知道如果的一个图内没有负环的图的情况，就不会在`i`到`j`这条路径上就不会出现相同的点。所以从i到k，在从k到j，要求i到j之间的最短路，就一定在这个最短路之间就不会出现重复的点。所以两边路径就一定是路径的，不会相互影响。

所以这里的状态转移方程的为`dist[k][i][j] = min(dist[k-1][i][j], dist[k-1][i][k] + dist[k-1][k][j])`

## 例题

### [AcWing 1125. 牛的旅行](https://www.acwing.com/problem/content/1127/)
农民John的农场里有很多牧区，有的路径连接一些特定的牧区。

一片所有连通的牧区称为一个牧场。

但是就目前而言，你能看到至少有两个牧区不连通。

现在，John想在农场里添加一条路径（注意，恰好一条）。

一个牧场的直径就是牧场中最远的两个牧区的距离（本题中所提到的所有距离指的都是最短的距离）。

考虑如下的两个牧场，每一个牧区都有自己的坐标：

！[](https://cdn.acwing.com/media/article/image/2019/10/30/19_2da2200cfa-1.png)

图 1 是有 5 个牧区的牧场，牧区用“*”表示，路径用直线表示。

图 1 所示的牧场的直径大约是 12.07106, 最远的两个牧区是 A 和 E，它们之间的最短路径是 A-B-E。

图 2 是另一个牧场。

这两个牧场都在John的农场上。

John将会在两个牧场中各选一个牧区，然后用一条路径连起来，使得连通后这个新的更大的牧场有最小的直径。

注意，如果两条路径中途相交，我们不认为它们是连通的。

只有两条路径在同一个牧区相交，我们才认为它们是连通的。

现在请你编程找出一条连接两个不同牧场的路径，使得连上这条路径后，所有牧场（生成的新牧场和原有牧场）中直径最大的牧场的直径尽可能小。

输出这个直径最小可能值。

输入格式
第 1 行：一个整数 N, 表示牧区数；

第 2 到 N+1 行：每行两个整数 X,Y， 表示 N 个牧区的坐标。每个牧区的坐标都是不一样的。

第 N+2 行到第 2*N+1 行：每行包括 N 个数字 ( 0或1 ) 表示一个对称邻接矩阵。

例如，题目描述中的两个牧场的矩阵描述如下：

  A B C D E F G H 
A 0 1 0 0 0 0 0 0 
B 1 0 1 1 1 0 0 0 
C 0 1 0 0 1 0 0 0 
D 0 1 0 0 1 0 0 0 
E 0 1 1 1 0 0 0 0 
F 0 0 0 0 0 0 1 0 
G 0 0 0 0 0 1 0 1 
H 0 0 0 0 0 0 1 0
输入数据中至少包括两个不连通的牧区。

输出格式
只有一行，包括一个实数，表示所求答案。

数字保留六位小数。

数据范围
1≤N≤150,
0≤X,Y≤105
输入样例：
```
8
10 10
15 10
20 10
15 15
20 15
30 15
25 10
30 10
01000000
10111000
01001000
01001000
01110000
00000010
00000101
00000010
```
输出样例：
```
22.071068
```

### 题解
题目要求我们求得所有的连通块之间的直径的最小值（最大值最小）
当我们连接两个连通块的时候将两个连通块合并成一个新的连通块，这时新的连通块的直径大小。
1. 新的连通块直径大小一定大于等于合并之前两个连通块各自直径的大小
2. 新的连通块，经过两连通块新连接的路径的最大路径长度。假设两个连通块之间的相连的路径节点分别为`x1` 和 `x2`，那么经过`x1` `x2`的这条路径的最长路径为，连通块`1`中离`x1`最远的距离 + $dist_{x1 \to x2}$ + 连通块`2`中离`x2`的最远距离

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
const double INF = 1e20;
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head
const int N = 155, M = N * N;

PII node[M];
double dist[N][N], maxd[N]; // maxd表示是距离这个点最远的距离
int n;
char g[N][N];


double get_dist(PII a, PII b)
{
    int dx = a.fi - b.fi;
    int dy = a.se - b.se;
    return sqrt(dx * dx + dy * dy);
}

int main()
{
    cin >> n;
    for(int i = 0; i < n; i ++)
    {
        int x, y;
        scanf("%d%d",&x,&y);
        node[i] = {x,y};
    }

    for(int i = 0; i < n; i ++)
        scanf("%s",g[i]);

    // init dist array
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < n; j ++)
        {
            if(g[i][j] == '1')
                dist[i][j] = get_dist(node[i],node[j]);
            else if(i != j)
                dist[i][j] = INF;
            else dist[i][j] = 0;
        }

    // floyd
    for(int k = 0; k < n; k ++)
        for(int i = 0; i < n; i ++)
            for(int j = 0; j < n; j ++)
                dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);

    // res1 find the area diameter
    // find the node which most farest of current node
    double res1 = 0;
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < n; j ++)
        {
            if(dist[i][j] < INF / 2 && i != j)
                maxd[i] = max(maxd[i], dist[i][j]); // diameter of the area
            res1 = max(res1, maxd[i]); // find the max min
        }

    double res2 = INF;
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < n; j ++)
            if(dist[i][j] > INF / 2 && i != j)
                res2 = min(res2, maxd[i]+ maxd[j] + get_dist(node[i], node[j]));

    printf("%0.6lf\n", max(res1, res2));

    return 0;
}
``

### [AcWing 343. 排序](www.acwing.com/problem/content/345/)
给定 n 个变量和 m 个不等式。其中 n 小于等于 26，变量分别用前 n 的大写英文字母表示。

不等式之间具有传递性，即若 A>B 且 B>C，则 A>C。

请从前往后遍历每对关系，每次遍历时判断：

如果能够确定全部关系且无矛盾，则结束循环，输出确定的次序；
如果发生矛盾，则结束循环，输出有矛盾；
如果循环结束时没有发生上述两种情况，则输出无定解。
输入格式
输入包含多组测试数据。

每组测试数据，第一行包含两个整数 n 和 m。

接下来 m 行，每行包含一个不等式，不等式全部为小于关系。

当输入一行 0 0 时，表示输入终止。

输出格式
每组数据输出一个占一行的结果。

结果可能为下列三种之一：

如果可以确定两两之间的关系，则输出 "Sorted sequence determined after t relations: yyy...y.",其中't'指迭代次数，'yyy...y'是指升序排列的所有变量。
如果有矛盾，则输出： "Inconsistency found after t relations."，其中't'指迭代次数。
如果没有矛盾，且不能确定两两之间的关系，则输出 "Sorted sequence cannot be determined."。
数据范围
2≤n≤26，变量只可能为大写字母 A∼Z。

输入样例1：
```
4 6
A<B
A<C
B<C
C<D
B<D
A<B
3 2
A<B
B<A
26 1
A<Z
0 0
```
输出样例1：
```
Sorted sequence determined after 4 relations: ABCD.
Inconsistency found after 2 relations.
Sorted sequence cannot be determined.`
``
输入样例2：
```
6 6
A<F
B<D
C<E
F<D
D<E
E<F
0 0
```
输出样例2：
```
Inconsistency found after 6 relations.
```
输入样例3：
```
5 5
A<B
B<C
C<D
D<E
E<A
0 0
```
输出样例3：
```
Sorted sequence determined after 4 relations: ABCDE.
```

### 题解
**传递闭包**