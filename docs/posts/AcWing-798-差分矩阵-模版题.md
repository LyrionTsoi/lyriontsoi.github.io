---
title: AcWing 798. 差分矩阵(模版题)
date: 2023-02-23 21:40:44
tags:
- 模版题
- AcWing
- 差分
categories:
- AcWing
- 蓝桥杯
- 差分
---

# 题意

输入一个 n 行 m 列的整数矩阵，再输入 q 个操作，每个操作包含五个整数 x1,y1,x2,y2,c，其中 (x1,y1) 和 (x2,y2) 表示一个子矩阵的左上角坐标和右下角坐标。

每个操作都要将选中的子矩阵中的每个元素的值加上 c。

请你将进行完所有操作后的矩阵输出。

**输入格式**

第一行包含整数 n,m,q。

接下来 n 行，每行包含 m 个整数，表示整数矩阵。

接下来 q 行，每行包含 5 个整数 x1,y1,x2,y2,c，表示一个操作。

**输出格式**

共 n 行，每行 m 个整数，表示所有操作进行完毕后的最终矩阵。

**数据范围**

1≤n,m≤1000,
1≤q≤100000,
1≤x1≤x2≤n,
1≤y1≤y2≤m,
−1000≤c≤1000,
−1000≤矩阵内元素的值≤1000

**输入样例**：

```
3 4 3
1 2 2 1
3 2 2 1
1 1 1 1
1 1 2 2 1
1 3 2 3 2
3 1 3 4 1
```

**输出样例**：

```
2 3 4 1
4 3 4 1
2 2 2 2
```

# 题解

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202302232210823.jpeg)

这里有两个点需要注意的是

1. 差分数组 `(x1,y1)`表示的是其右下角的所有操作之和（如上图所示）
2. 要求最后的输出矩阵就需要计算前缀和

# CODE

```c++
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
const int N = 1010, M = 1010;

int a[N][N], b[N][N];
int n,m;
int q;

void insert(int x1,int y1, int x2, int y2, int c)
{
    b[x1][y1] += c;
    b[x2 + 1][y1] -= c;
    b[x1][y2 + 1] -= c;
    b[x2 + 1][y2 + 1] += c;
}

int main()
{
    cin >> n >> m >> q;

    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= m; j ++)
        {
            scanf("%d",&a[i][j]);
            // a是b的前缀和，b是a的差分数组
            insert(i,j,i,j,a[i][j]);
        }


    while(q --)
    {
        int x1, y1, x2, y2,c;
        cin >> x1 >> y1 >> x2 >> y2 >> c;
        insert(x1,y1,x2,y2,c);
    }

    // 构造出最后的矩阵
    for(int i = 1; i <= n; i ++)
    {    
        for(int j = 1; j <= m; j ++)
        {
            b[i][j] +=  b[i-1][j] + b[i][j-1] - b[i-1][j-1];
            cout << b[i][j] << ' ';
        }
        puts("");
    }

    return 0;
}
```

