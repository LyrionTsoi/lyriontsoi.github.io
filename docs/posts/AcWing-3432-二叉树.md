---
title: AcWing 3432. 二叉树
date: 2023-05-09 22:12:00
tags:
---

## [题意](https://www.acwing.com/problem/content/3435/)

定义了一个新的概念找公共父节点，利用满二叉树的编号的性质可以一直往上找到其直接的父节点

## CODE

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
const int N = 40;

int path1[N], path2[N];
int sz1, sz2;


void find(int u,int path[],int &sz){
    if(u < 1) return;
    // cout << u << ' ' << sz << endl;
    path[sz ++] = u;
    // cout << u << ' ' << endl;
    find(u / 2, path,sz);
}

int main()
{
    int a, b;
    cin >> a >> b;

    if(a < b)
    {
        int t = a;
        a = b;
        b = t;
    }

    find(a, path1, sz1);
    find(b, path2, sz2);

    sort(path1, path1 + sz1);
    sort(path2, path2 + sz2);

    // for(int i = 0; i < sz1; i ++)
    //     cout << path1[i] << ' ';
    // puts("");

    // for(int i = 0; i < sz2; i ++)
    //     cout << path2[i] << ' ';
    // puts("");

    // cout << sz1 << ' ' << sz2 << endl;


    // puts("");
    // for(int i = 0; i < sz2; i ++)
    //     cout << path2[i] << ' ';
    // puts("");

    int loc = 0;
    while(path1[loc] == path2[loc] && loc < sz1 && loc < sz2) loc ++;
    // cout << loc << endl;
    cout << path2[loc - 1];

    // cout << 1 << endl;
    return 0;
}
```

