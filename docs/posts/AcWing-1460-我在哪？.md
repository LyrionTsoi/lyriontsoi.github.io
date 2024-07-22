---
title: AcWing 1460. 我在哪？
date: 2023-02-28 20:23:59
tags:
- AcWing 
- 二分
- 蓝桥
categories:
- AcWing
- 蓝桥
- 二分
---

# [题意](https://www.acwing.com/problem/content/1462/)

农夫约翰出门沿着马路散步，但是他现在发现自己可能迷路了！

沿路有一排共 N 个农场。

不幸的是农场并没有编号，这使得约翰难以分辨他在这条路上所处的位置。

然而，每个农场都沿路设有一个彩色的邮箱，所以约翰希望能够通过查看最近的几个邮箱的颜色来唯一确定他所在的位置。

每个邮箱的颜色用 A..Z 之间的一个字母来指定，所以沿着道路的 N 个邮箱的序列可以用一个长为 N 的由字母 A..Z 组成的字符串来表示。

某些邮箱可能会有相同的颜色。

约翰想要知道最小的 K 的值，使得他查看任意连续 K 个邮箱序列，他都可以唯一确定这一序列在道路上的位置。

例如，假设沿路的邮箱序列为 `ABCDABC` 。

约翰不能令 K=3，因为如果他看到了 `ABC`，则沿路有两个这一连续颜色序列可能所在的位置。

最小可行的 K 的值为 K=4，因为如果他查看任意连续 4 个邮箱，那么可得到的连续颜色序列可以唯一确定他在道路上的位置。

**输入格式**

输入的第一行包含 N，第二行包含一个由 N 个字符组成的字符串，每个字符均在 A..Z 之内。

**输出格式**

输出一行，包含一个整数，为可以解决农夫约翰的问题的最小 K 值。

**数据范围**

1≤N≤100

**输入样例**：

```
7
ABCDABC
```

**输出样例**：

```
4
```

# 题解

**暴力**

第一维枚举我们的答案，一旦找到第一个K符合就是我们最后需要寻找的答案。（k从小到大进行枚举）

**二分 + 哈希存储**

二分查找答案

​	证明答案序列具有二段性

​	![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202303022034216.jpeg)

字串使用哈希表存储

# CODE

**暴力**

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
const int N = 105;

int n;
string str;

int main()
{
    cin >> n >> str;

    // 暴力
    // 首先枚举的是ans 最小区分前缀
    for(int k = 1; k <= n; k ++)
    {
        bool flag = false;
        // 枚举第一个字串的开始地方
        for(int i = 0; i + k - 1< n; i ++)
        {
            for(int j = i + 1; j + k - 1 < n; j ++)
            {   
                bool same = true;
                // 比较两个字串是否相同
                for(int u = 0; u < k; u ++)
                {
                    // 有一个字符不一样的话表示着两个字符串就是不一样，但是有一样的字符不代表的字符串就可以匹配
                    if(str[j + u] != str[i + u])
                    {
                        same = false;
                        break;
                    }
                }

                if(same) 
                {
                    flag = true;
                    break;
                }

            }
            if(flag) break;
        }
        if(!flag)
        {
            cout << k << endl;
            return 0;
        }
    }
}
```

**二分+哈希优化**

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
const int N = 105;

int n;
string str;
unordered_set<string> S; // 用哈希表存放

bool check(int mid)
{
    S.clear();
    for(int i = 0; i + mid - 1 < n; i ++)
    {
        string s = str.substr(i,mid);
        if(S.count(s)) return false;
        S.insert(s);
    }
}

int main()
{
    cin >> n >> str;

    // 二分找k（后面可以k具有二段性）
    // 使用哈希查找字符串是否存在
    int l = 1, r = n;
    while(l < r)
    {
        int mid = l + r >> 1;
        // 这里是检查的是否符合最短唯一字串
        if(check(mid)) r = mid;
        else l = mid + 1;
    }

    cout << l << endl;
}
```



