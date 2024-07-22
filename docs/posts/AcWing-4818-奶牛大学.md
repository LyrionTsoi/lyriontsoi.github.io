---
title: AcWing 4818. 奶牛大学
date: 2022-12-31 13:47:56
tags:
- 枚举
---

# [题目描述](https://www.acwing.com/problem/content/4821/)

Farmer John 计划为奶牛们新开办一所大学！

有 N 头奶牛可能会入学。

每头奶牛最多愿意支付 $c_i$ 的学费。

Farmer John 可以设定所有奶牛入学需要支付的学费。

如果这笔学费大于一头奶牛愿意支付的最高金额，那么这头奶牛就不会入学。

Farmer John 想赚尽可能多的钱，从而可以给他的讲师提供一笔可观的工资。

请求出他能赚到的钱的数量，以及此时应当收取多少学费。

## 输入格式

输入的第一行包含 N。

第二行包含 N 个整数 c1,c2,…,cN，其中 $c_i$ 是奶牛 i 愿意支付的最高学费金额。

## 输出格式

输出 Farmer John 可以赚到的最大金额以及最优情况下他应该收取的学费。如果有多个解，输出收取学费最小的解。

注意这个问题涉及到的整数可能需要使用 6464 位整数型（例如，Java 中的 “long”，C/C++ 中的 “long long”）。

## 数据范围

1≤N≤ $10^5$,
1≤ci≤ $10^6$。

## 输入样例：

```
4
1 6 4 6
```

## 输出样例：

```
12 4
```

## 样例解释

如果 Farmer John 收费 4，那么 3 头奶牛将会入学，从而使他赚取 3×4=123×4=12 的金额。

# 题解

**枚举方式：**

* 存储方式：使用数组，从下标为一的位置开始存储
* 对数组进行从大到小进行排序
* 然后从头到尾遍历数组，比较当前的这个人做为学费能收取到的总学费，找出来所有总学费中的最大值。如果最大值相同选学费最便宜的，即人最多的。在我们的程序里体现为`if(res <= arr[i] * i)`,

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

int arr[N];
int n;

bool cmp(int a, int b)
{
    return a > b;
}

int main()
{
    cin >> n;

    for(int i = 1; i <= n; i ++)
        cin >> arr[i];

    sort(arr + 1, arr + n + 1, cmp);

    ll res = 0;
    ll pay = 0;
    for(int i = 1; i <= n; i ++)
        if(res <= (ll)arr[i] * i)
        {
            res = (ll)arr[i] * i;
            pay = arr[i];
        }

    cout << res << ' ' << pay << endl;
    return 0;
}
```

