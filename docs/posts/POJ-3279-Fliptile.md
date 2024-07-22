---
title: POJ-3279 Fliptile
date: 2022-10-18 17:02:43
tags:
- 搜索
- 思维
categories:
- kuangbin
- 搜索
---

## [POJ-3279 Fliptile](https://vjudge.net/problem/POJ-3279)
夫约翰知道，一头知识上满意的母牛是一头快乐的母牛，它将提供更多的牛奶。他为母牛安排了脑力活动，他们在其中操纵M × N 网格（1≤ M ≤15； 1≤ N ≤15）个正方形瓷砖，每个瓷砖的一面都涂成黑色，另一面则涂成白色。

正如人们所猜测的那样，当翻转单个白色瓷砖时，它会改变变黑翻转单个黑色图块时，它会变为白色。母牛在翻转砖块时会得到奖励，因此每块砖块的白色侧面都朝上。但是，母牛的蹄子相当大，当他们尝试翻转特定的砖块时，它们也会翻转所有相邻的砖块（与翻转后的砖块共享整个边缘的砖块）。由于翻转很累，奶牛希望尽量减少必须做的翻转次数。

帮助母牛确定所需的最小翻转次数，并确定要达到最小翻转次数的位置。如果有多种方法可以以最少的翻转次数来完成任务，则当将其视为字符串时，以输出中词典顺序最少的方式返回。如果无法完成任务，请用“ IMPOSSIBLE”一词打印一行。

Input
第1行：两个以空格分隔的整数： M 和 N
第2 .. M +1行：第 i +1行描述了网格中第i行（ N 以空格分隔的整数，黑色为1，白色为0。
Output
第1 .. M 行：每行包含 N 个以空格分隔的整数，每个整数指定翻转该特定位置的次数。

Sample Input
```
4 4
1 0 0 1
0 1 1 0
0 1 1 0
1 0 0 1
```
Sample Output
```
0 0 0 0
1 0 0 1
1 0 0 1
0 0 0 0
```

## 题解
**二进制压缩**
这里压缩状态意义是：**每一位是否翻转**

如： 101 表示的第1位和第三位进行翻转，假如一共有三位，根据组合数学知道一共会有2^3 - 1个组成方式，我们即可以从000枚举到111(0表示没有对这个位置进行翻转，1表示对这个位置进行翻转)

**我们还要知道一点是，只要第一行确定了，下面的翻转的方案也就固定了**

如：第一行为101 应为要保证全0所以下一行的对应上一行1位置要进行翻转

本题是细节非常多！！

比如
（1）我们要清楚知道这里的状态压缩压缩的状态所代表的意义  
（2）方案不一定成立，所以要检查枚举完最后的一行是否全0

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
const int N = 20;

// g为原图， test是通过每一次枚举第一行的方案进行测试
// save表示的是我们存下的最优方案（翻转次数最小且字典序最小）, backup表示的是当前存储的改变次数的数组
int g[N][N], test[N][N];
int save[N][N], backup[N][N];
int dx[] = {0,0,1,-1,0}, dy[] = {1,-1,0,0,0};
int n, m, res,cnt;

void copyArr(int test[][N], int g[][N])
{
    for(int i = 0; i < m; i ++)
        for(int j = 0; j < n; j ++)
            test[i][j] = g[i][j];
}


int dfs(int x)
{
    // for(int i = 0; i < n; i ++)
    //         cout << test[x-1][i] << " \n"[i == n-1];
    if(x == m)
    {
        // cout << cnt << endl;
        // cout << "test" << endl;
        // for(int i = 0; i < m; i ++)
        //     for(int j = 0; j < n; j ++)
        //         cout << test[i][j] << " \n"[j == n-1];
        // 检测一下最后一行是否全为0
        

        for(int i = 0; i < n; i ++)
            if(test[x-1][i] == 1) return INF;

        // cout << cnt << endl;
        return cnt;
    }

    for(int i = 0; i < n; i ++)
    {
        if(test[x-1][i])
        {
            cnt ++;
            backup[x][i] ++;
            
            // 翻转
            for(int j = 0; j < 5; j ++)
            {
                int cx = x + dx[j], cy = i + dy[j];
                if(cx >= 0 && cx < m && cy >= 0 && cy < n)
                    test[cx][cy] ^= 1;
            }
        }
    }

    return dfs(x + 1);
}

void init()
{
    // 枚举第一行的所有可能性
    // 我们要知道一件事的就是无论的初始状态是什么，都能经过有限次的组合变化变沉另外一种状态
    memset(save , 0, sizeof save);
    res = INF;
    for(int k = 0; k < (1 << n); k ++) // 二进制压缩状态
    {
        // cout << k << endl;
        copyArr(test, g);
        memset(backup, 0, sizeof backup);
        
        // puts("");
        // puts("");
        // cout << "save" << endl;
        // for(int i = 0; i < m; i ++)
        //     for(int j = 0; j < n; j ++)
        //         cout << save[i][j] << " \n"[j == n-1];
        // puts("");
        // puts("");

        cnt = 0;
        for(int i = 0; i < n; i ++)
        {
            if(k >> i & 1)
            {
                cnt ++;

                for(int j = 0; j < 5; j ++)
                {
                    int cx = 0 + dx[j], cy = (n - i - 1) + dy[j];
                    if(cx >= 0 && cx < m && cy >= 0 && cy < n)
                    test[cx][cy] ^= 1;
                }

                backup[0][n - i - 1] ++;
            }
        }
        // cout << "test" << endl;
        // for(int i = 0; i < m; i ++)
        //     for(int j = 0; j < n; j ++)
        //         cout << test[i][j] << " \n"[j == n-1];

        int t = dfs(1);
        // cout << t << endl;
        if(res > t)
        {
            // cout << cnt << endl;  
            for(int i = 0; i < m; i ++)
                for(int j = 0; j < n; j ++)
                    save[i][j] = backup[i][j];
            res = t;
        }
    }

}

int main()
{
    cin >> m >> n;

    for(int i = 0; i < m; i ++)
        for(int j = 0; j < n; j ++)
            cin >> g[i][j];

    // for(int i = 0; i < m; i ++)
    //         for(int j = 0; j < n; j ++)
    //             cout << g[i][j] << " \n"[j == n-1];b

    init();

    // cout << "save" << endl;
    if(res == INF) puts("IMPOSSIBLE");
    else
    {    
        for(int i = 0; i < m; i ++)
            for(int j = 0; j < n; j ++)
                cout << save[i][j] << " \n"[j == n-1];
    }


    
    return 0;
}
```