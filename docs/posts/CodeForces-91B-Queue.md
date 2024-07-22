---
title: CodeForces - 91B Queue
date: 2022-07-31 14:04:31
tags:
- 二分
- 线段树
categories:
- CodeForces
- - 二分
- - 线段树
---

## [CodeForces - 91B Queue](https://vjudge.net/problem/CodeForces-91B)
There are n walruses standing in a queue in an airport. They are numbered starting from the queue's tail: the 1-st walrus stands at the end of the queue and the n-th walrus stands at the beginning of the queue. The i-th walrus has the age equal to ai.

The i-th walrus becomes displeased if there's a younger walrus standing in front of him, that is, if exists such j (i < j), that ai > aj. The displeasure of the i-th walrus is equal to the number of walruses between him and the furthest walrus ahead of him, which is younger than the i-th one. That is, the further that young walrus stands from him, the stronger the displeasure is.

The airport manager asked you to count for each of n walruses in the queue his displeasure.

Input
The first line contains an integer n (2 ≤ n ≤ 105) — the number of walruses in the queue. The second line contains integers ai (1 ≤ ai ≤ 109).

Note that some walruses can have the same age but for the displeasure to emerge the walrus that is closer to the head of the queue needs to be strictly younger than the other one.

Output
Print n numbers: if the i-th walrus is pleased with everything, print "-1" (without the quotes). Otherwise, print the i-th walrus's displeasure: the number of other walruses that stand between him and the furthest from him younger walrus.

Sample 1
Input
```	
6
10 8 5 3 50 45
```
Output
```
2 1 0 -1 0 -1 
```
Sample 2
Input
```	
7
10 4 6 3 2 8 15
```
Output
```
4 2 1 0 -1 -1 -1 
```
Sample 3
Input
```
5	
10 3 1 10 11
```
Output
```
1 0 -1 -1 -1
```

### 题解
本题要我们求每个数的右边最远的比自己小的数字
很明显本题可以通过二分找出右边第一个比当前数字要小的位置。但是二分必须要满足二段性，所以我们需要先预处理一遍数组，使得数字满足二义性。
本题预处理的数从数组末尾开始处理的当前位置右边最小的数值是什么。

### CODE
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
typedef double db;
const ll mod=1000000007;
const int INF = 0x3f3f3f3f;
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head
const int N = 200010; 

int a[N];
int s[N];

int main()
{
    int n;
    cin >> n;
    for(int i = 1; i <= n; i ++)
        scanf("%d",&a[i]);

    // 一定要看清楚题意！！！
    // 我们应该要往右找到最后一个比当前的值要小的数值，并且答案 = r - l
    // 要想使用二分搜索就一定要求有二段性
    memset(s,0x3f,sizeof s);
    // s[i]表示从n~i之间的最小值，从1～n顺序去看是非严格下降的
    for(int i = n; i >= 1; i --)
        s[i] = min(s[i+1], a[i]);

    for(int i = 1; i <= n; i ++)
    {
        int l = i, r = n;
        // 整数二分
        while(l < r)
        {
            int mid = l + r + 1>> 1;
            // 要找从右边开始的第一个比a[i]小的数
            if(s[mid] < a[i]) l = mid;  
            else r = mid - 1;
        }
        printf("%d ", l - i - 1);
    }
    return 0;
}
```