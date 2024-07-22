---
title: AcWing 797. 差分（模版题）
date: 2023-02-23 20:57:17
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

输入一个长度为 n 的整数序列。

接下来输入 m 个操作，每个操作包含三个整数 l,r,c，表示将序列中 [l,r] 之间的每个数加上 c。

请你输出进行完所有操作后的序列。

**输入格式**

第一行包含两个整数 n 和 m。

第二行包含 n 个整数，表示整数序列。

接下来 m 行，每行包含三个整数 l，r，c，表示一个操作。

**输出格式**

共一行，包含 n 个整数，表示最终序列。

**数据范围**

1≤n,m≤100000,
1≤l≤r≤n,
−1000≤c≤1000,
−1000≤整数序列中元素的值≤1000

**输入样例**：

```
6 3
1 2 2 1 2 1
1 3 1
3 5 1
1 6 1
```

**输出样例**：

```
3 4 5 3 4 2
```

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
const int N = 100010;

int a[N], b[N]; // b 是差分数组，因为原数组存放了数据所以不能在原数组基础上进行差分操作
int n, m;

int main()
{
    cin >> n >> m;

    for(int i = 1;i <= n; i++)
        scanf("%d",&a[i]);

    for(int i = 1; i <= m; i ++)
    {
        int l,r,c;
        cin >> l >> r >> c;
        b[l] += c, b[r + 1] -= c;
        // puts("");
        // cout << l << ' ' << a[l] << ' ' << r+1 << ' ' << a[r + 1] << endl;
    }

    // output the anwser
    for(int i = 1; i <= n; i++)
    {
        b[i] += b[i-1];
        a[i] += b[i];
        cout << a[i] << ' ';
    }

    return 0;
}
```

