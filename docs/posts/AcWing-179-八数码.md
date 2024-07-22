---
title: AcWing 179. 八数码(A-star算法模版)
date: 2022-09-15 19:35:01
tags:
- 搜索
- A-start
categories:
- 模版
- 搜索
- A-star
---

## [AcWing 179. 八数码(A-star算法模版)](https://www.acwing.com/problem/content/181/)
在一个 3×3 的网格中，1∼8 这 8 个数字和一个 X 恰好不重不漏地分布在这 3×3 的网格中。

例如：
```
1 2 3
X 4 6
7 5 8
```
在游戏过程中，可以把 X 与其上、下、左、右四个方向之一的数字交换（如果存在）。

我们的目的是通过交换，使得网格变为如下排列（称为正确排列）：
```
1 2 3
4 5 6
7 8 X
```
例如，示例中图形就可以通过让 X 先后与右、下、右三个方向的数字交换成功得到正确排列。

交换过程如下：
```
1 2 3   1 2 3   1 2 3   1 2 3
X 4 6   4 X 6   4 5 6   4 5 6
7 5 8   7 5 8   7 X 8   7 8 X
```
把 X 与上下左右方向数字交换的行动记录为 u、d、l、r。

现在，给你一个初始网格，请你通过最少的移动次数，得到正确排列。

输入格式
输入占一行，将 3×3 的初始网格描绘出来。

例如，如果初始网格如下所示：
```
1 2 3 
x 4 6 
7 5 8 
```
则输入为：`1 2 3 x 4 6 7 5 8`

输出格式
输出占一行，包含一个字符串，表示得到正确排列的完整行动记录。

如果答案不唯一，输出任意一种合法方案即可。

如果不存在解决方案，则输出 unsolvable。

输入样例：
```
2  3  4  1  5  x  7  6  8 
```
输出样例
```
ullddrurdllurdruldr
```

## 题解
A-Star 算法实际上就是基于dijkstra的演变

A*算法需要一个估价函数，这个估计函数估计的是当前状态节点到目标状态节点的之间的估计代价值。
**估计函数的要求**：`一定要比实际上该点数值到目标状态点的数值要小`
小根堆内维护的是实际上当前的节点到初始节点的实际距离+当前节点到目标节点的估计距离

**Astart算法的重要结论**
* 所有的节点可能会进队多次，出队多次。但目标节点只会出队一次，且出队得到的数值是最优解。
* 出队即为最优值是针对的是终点来说

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
typedef pair<int,string> PIS;
typedef double db;
const ll mod=1000000007;
const int INF = 0x3f3f3f3f;
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head

// 估计函数， 使用曼哈顿距离进行估计
// 曼哈顿距离是在网格图上的最短距离，所以这个估计值一定是比实际值要小于等于的
int h(string state)
{
    int res = 0;
    for(int i = 0; i < state.size(); i ++)
        if(state[i] != 'x')
        {
            int t = state[i] - '1';
            // 曼哈顿距离
            res += abs(i/3 - t/3) + abs(i%3 - t%3);
        }
    return res;
}

string astar(string start)
{
    int dx[4] = {0,0,1,-1}, dy[4] = {1,-1,0,0};
    char op[4] = {'r', 'l', 'd','u'};

    string end = "12345678x";
    // dist[state] 表示state这个状态距离初始状态的最短路径
    unordered_map<string,int> dist;
    unordered_map<string, pair<string,char>> prev;
    priority_queue<PIS,vector<PIS>, greater<PIS>> heap;
    dist[start] = 0;
    heap.push({h(start),start});

    while(heap.size())
    {
        auto t = heap.top();
        heap.pop();

        string state = t.second;
        int cost = dist[state]; // 当前点到源点的最短距离

        // 找到目标状态直接剪枝
        if(state == end) break;

        // 找出空格的位置，在空的坐标上下左右都可以向这个空格移动 
        int x,y; 
        for(int i = 0; i < state.size(); i ++)
            if(state[i] == 'x')
            {
                x = i / 3;
                y = i % 3;
                break;
            }

        // 对这个空格坐标上下左右进行更新
        string source = state;
        for(int i = 0; i < 4; i ++)
        {
            int cx = x + dx[i], cy = y + dy[i];
            // 不合法状态
            if(cx < 0 || cx >= 3 || cy < 0 || cy >= 3) continue;
            // 拓展节点
            state = source;
            swap(state[x * 3 + y], state[cx * 3 + cy]);
            // 如果当前的节点没有被更新或当前节点到源点的距离需要更新
            if(!dist.count(state) || dist[state] > cost + 1) 
            {
                dist[state] = cost + 1;
                // prev[state] 表示状态为state，前驱状态为source, 转移的操作为 op[i]
                prev[state] ={source,op[i]};
                heap.push({dist[state] + h(state),state});
            }
        }
    }

    string res;
    while(end != start)
    {
        res += prev[end].second;
        end = prev[end].first;
    }
    reverse(res.begin(), res.end());
    return res;
}

int main()
{
    // 将所有的状态表示从一维的形式，存放在g中
    // seq 是为了计算出来当前的初始状态->目标状态是否有解
    // 有解的情况:普通的情况是初始状态和目标状态之间的逆序对的奇偶性不发生变化
    // 在这里目标状态的逆序对为0，即当初始状态为逆序对偶数的时候有解，否则反之
    string g, c, seq;
    int i = 0;
    while(cin >> c)
    {
        g += c;
        if(c != "x") seq += c;
    }

    // 判断逆序对的奇偶性
    // 通用的解法
    int t = 0;
    for(int i = 0; i < seq.size(); i ++)
        for(int j = i + 1; j < seq.size(); j ++)
            if(seq[i] > seq[j])
                t ++;

    if(t % 2) puts("unsolvable");
    else cout << astar(g) << endl;

    return 0;
}
```
