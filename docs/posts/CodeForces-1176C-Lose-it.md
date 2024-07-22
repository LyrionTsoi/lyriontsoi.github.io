---
title: CodeForces 1176C - Lose it
date: 2023-03-30 16:12:34
tags:
- 思维
---

# [题目描述](https://vjudge.net/problem/CodeForces-1176C#author=beyoursven)

# 题解

这个题用 O(n) 的方法去求解，要使良好的数组成立，必须使这6个数依次出现，可以不连续，**我用map依次映射4 8 15 16 23 42 为1-6，用ans[]表示良好数组中这些数的出现次数，即ans中的数都是已经添加在良好数组中的数。**
如果输入的是4时 ans[mp[4]]++，如果不是，则要判断比他小的那一个数出现次数是不是大于当前数，如果是才能++。
比如：要想把15这个数添加到良好数组中，数组中必须要已经有4和8。即**前面的数出现次数高于这个数时才能++，如果能顺利加到 42 ，则说明良好序列的数量+1**，最后输出n-ans[6]*6的值即可。

[官方题解](https://codeforces.com/blog/entry/67598)

# CODE

```c++
#include <bits/stdc++.h>
using namespace std;
#define PI 3.14159265359
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
const int N = 8, M = 500010;

int ans[N], a[M];
map<int,int> key;

int main()
{
    key[4] = 1;
    key[8] = 2;
    key[15] = 3;
    key[16] = 4;
    key[23] = 5;
    key[42] = 6;


    int n;cin >> n;
    for(int i = 0; i < n;i ++)
    {
        cin >> a[i];
        // 遇到 4 就加入到的队列里
        // 否则遇到其他就要看相邻的比他小的那个数出现的次数是否大于当前这个数
        if(a[i] == 4) ans[key[4]] ++;
        else if(ans[key[a[i]] - 1] > ans[key[a[i]]] )
            ans[key[a[i]]] ++;
    }

    cout << n - 6 * ans[6] << endl;

    return 0;
}
```

