---
title: AcWing 1082. 数字游戏
date: 2022-10-08 20:51:49
tags:
- 数位dp
- 动态规划
categories:
- AcWing
- 动态规划
- 数位dp
---

## AcWing 1082. 数字游戏
科协里最近很流行数字游戏。

某人命名了一种不降数，这种数字必须满足从左到右各位数字呈非下降关系，如 123，446。

现在大家决定玩一个游戏，指定一个整数闭区间 [a,b]，问这个区间内有多少个不降数。

输入格式
输入包含多组测试数据。

每组数据占一行，包含两个整数 a 和 b。

输出格式
每行给出一组测试数据的答案，即 [a,b] 之间有多少不降数。

数据范围
$1≤a≤b≤2^{31}−1$
输入样例：
```
1 9
1 19
```
输出样例：
```
9
18
```

### 题解
我们根据数值知道这是一个32位数的组合型问题，正常的寻找如果按数值大小的寻找枚举一遍至少是$2^{32} = 4 * 10^9$ 级别。所以现在考虑是否将这个n改变成按位查询，即使用数位dp的方式。

数组F[i][j]: 表示一共有i位且最高位为j的所有不降数的方案数。

初始化数组F[i][j]，一般有两种办法。  
    (1) 使用组合数形式  
    (2) 使用递归形式

对于本题来说的F[i][j] = 所有满足第i-1位上k>=j的所有方案，即
$$F[i][j] = \sum_{k=j}^9 F[i-1][k]$$

数位dp的考虑形式  
因为数位dp一般都是从0
对于每一位都考虑两种情况（1）0～lim - 1 （2） lim。这个lim是我们讨论的(0,n),n在这一位上的数。


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
const int N = 35;

int f[N][10]; // (f[i][j],i为最高位填的是j)

void init()
{
    for(int i = 0; i <= 9; i ++) f[1][i] = 1;

    for(int i = 2; i <= N; i ++)
        for(int j = 0; j <= 9; j ++)
            for(int k = j; k <= 9; k ++)
                f[i][j] += f[i-1][k];
}

int dp(int n)
{
    if(!n) return 1;

    vector<int> nums;
    while(n) nums.pb(n % 10), n /= 10;

    int res = 0, last = 0;
    for(int i = nums.size() - 1; i >= 0; i --)
    {
        int x = nums[i];
        for(int j = last; j < x; j ++)
            res += f[i + 1][j];

        if(x < last) break;
        last = x;

        if(!i) res ++;
    }
    return res;
}

int main()
{
    int l, r;
    init();
    while(cin >> l >> r)
    {   
        cout << dp(r) - dp(l-1) << endl;
    }

    return 0;
}
```