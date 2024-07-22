---
title: AcWing 3956. 截断数组
date: 2023-02-21 20:02:47
tags:
- Acwing
- 前缀和
Categories:
- Acwing
- 蓝桥杯
- 前缀和
---

# AcWing 3956. 截断数组

[AcWing 3956. 截断数组](https://www.acwing.com/problem/content/3959/)

给定一个长度为 n 的数组 a1,a2,…,an。

现在，要将该数组从中间截断，得到三个**非空**子数组。

要求，三个子数组内各元素之和都相等。

请问，共有多少种不同的截断方法？

**输入格式**

第一行包含整数 n。

第二行包含 n 个整数 a1,a2,…,an。

**输出格式**

输出一个整数，表示截断方法数量。

**数据范围**

前六个测试点满足 1≤n≤10。
所有测试点满足 1≤n≤105，−10000≤ai≤10000。

**输入样例1：**

```
4
1 2 3 3
```

**输出样例1：**

```
1
```

输入样例2：

```
5
1 2 3 4 5
```

**输出样例2：**

```
0
```

**输入样例3：**

```
2
0 0
```

**输出样例3：**

```
0
```

# 题解

构造前缀和的，采用枚举第二点的方法实现在时间复杂度为O(n)的算法

具体讲解枚举方式

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202302212050480.png)

假设上图是第二节点的分割位置，每个点都可以和前j-1个点中合法的点进行组合。

这里具体说的合法点是假设j-1个点中第i个点的前缀和为 $\frac{s}{3}$,这里是根据总和为s，要分成三个相同的数组，那么每一个数组之和一定是固定的并且为 $\frac{s}{3}$.

在枚举第二点的过程当中，记录当前点j之前有多少个s/3，有多少个的s/3点就意味着可以和多少个这个点进行组合。

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

int a[N], s[N]; // a[]原数组, s[]前缀和数组
int n;

int main()
{
    cin >> n;
    for(int i = 1; i <= n; i ++)
    {
        scanf("%d",&a[i]);
        s[i] = s[i-1] + a[i];
    }

    if(s[n] % 3)
    {   
        puts("0");
        return 0;
    }


    // 利用前缀和枚举数组的排列情况
    // 暴力枚举
    // int s1 = 0, ans = 0;
    // for(int i = 1; i < n - 1 ;i ++)
    // {
    //     s1 = s[i];
    //     for(int j = i + 1; j < n; j ++)
    //     {
    //         if(s[j] - s[i] == s1 && s[n] - s[j] == s1)
    //             ans ++;
    //     }
    // }

    // 利用这里的特性
    // 由于我们需要分成三个非空数组，且每一个非空数组都相同，那么就意味着每个数组只之和=s/3
    // 这里我们采用枚举第二个点j，并在过程中记录当前点j之前有多少个s/3，有多少个的s/3点就意味着可以和多少个这个点进行组合
    int cnt = 0;
    ll ans = 0; // cnt记录的是j点之前有多少个s/3点
    int s1 = s[n];
    for(int j = 2;j < n; j ++)
    {
        if(s[j - 1] == s1 / 3)
            cnt ++;
        if(s[j] == (2 * s1) / 3)
            ans += cnt;
    }


    cout << ans << endl;

    return 0;
}
```

