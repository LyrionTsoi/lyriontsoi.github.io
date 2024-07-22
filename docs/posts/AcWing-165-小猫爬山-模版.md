---
title: AcWing 165. 小猫爬山(模版)
date: 2022-11-02 14:50:36
tags:
- dfs
- 剪枝
categories:
- AcWing
- 搜索
- dfs剪枝
---

## [AcWing 165. 小猫爬山(模版)](https://www.acwing.com/problem/content/description/167/)
翰翰和达达饲养了 N 只小猫，这天，小猫们要去爬山。

经历了千辛万苦，小猫们终于爬上了山顶，但是疲倦的它们再也不想徒步走下山了（呜咕>_<）。

翰翰和达达只好花钱让它们坐索道下山。

索道上的缆车最大承重量为 W，而 N 只小猫的重量分别是 C1、C2……CN。

当然，每辆缆车上的小猫的重量之和不能超过 W。

每租用一辆缆车，翰翰和达达就要付 1 美元，所以他们想知道，最少需要付多少美元才能把这 N 只小猫都运送下山？

输入格式
第 1 行：包含两个用空格隔开的整数，N 和 W。

第 2..N+1 行：每行一个整数，其中第 i+1 行的整数表示第 i 只小猫的重量 Ci。

输出格式
输出一个整数，表示最少需要多少美元，也就是最少需要多少辆缆车。

数据范围
1≤N≤18,
1≤Ci≤W≤108
输入样例：
```
5 1996
1
2
1994
12
29
```
输出样例：
```
2
```

## 题解
在这里的我们的枚举顺序是按猫进行枚举
在这里的举个列一共有10只猫，我们这里的枚举顺序是
假设从第二只猫开始枚举，应为第一只猫一定是新创一个缆车，让猫乘坐。那么对于第二只猫来说，这一只猫可以选择前面的猫做的缆车，也可以自己再开一辆车坐。随着枚举的猫越多，我们开辟的缆车就越多。剪枝的策略就是让当前节点的拓展分支越少越好，所以我们先按越重的越先上缆车。因为这样的话，当前缆车能上的猫的可能性就越小，所以对于后面的选择就会越小，所以分支数也就会越小。如果在前面的剪枝的话就能剪掉更多的分支数目。

## CODE
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
const int N = 20;

int n, m;
int w[N];
int sum[N]; // 记录每辆缆车已经承重多少
int res = N;

bool cmp(int a, int b)
{
    return a > b;
}

// cost 既可以表示现在的花销也可以表示的意思是缆车的数目（因为一辆缆车一元）
void dfs(int nu, int cost)
{

    if(cost >= res) return; // 最优解剪枝
    if(nu >= n) // 可行性剪枝
    {
        res = cost;
        return;
    }

    // 上某一缆车
    for(int i = 0; i < cost; i ++)
        // 可行性剪枝
        if(sum[i] + w[nu] <= m) // 这里的不用判断是否空车，如果换成不同价格的缆车还需要传递参数
        {
            sum[i] += w[nu];
            dfs(nu + 1, cost);
            sum[i] -= w[nu];
        }

    // 自己一猫一车
    sum[cost] = w[nu];
    dfs(nu + 1, cost + 1);
    sum[cost] = 0; // 无论哪一种递归方式都要
}

int main()
{
    cin >> n >> m;
    for(int i = 0; i < n; i ++) cin >> w[i];

    sort(w, w + n, cmp);
    
    dfs(0,0);

    cout << res << endl;

    return 0;
}
```