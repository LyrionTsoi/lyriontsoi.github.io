---
title: UESTC - 624 整数划分
date: 2022-09-01 14:59:05
tags:
- 数论
- 生成函数
categories:
- UESTC
- 数论
- 生成函数
---

## [UESTC - 624 整数划分](https://vjudge.net/problem/UESTC-624#author=0)
一个整数可以写成多个整数相加的形式，比如44可以写成以下形式

4=4

4=3+1

4=2+2

4=2+1+1

4=1+1+1+1

注意 4=3+1和 4=1+3是一样的，现在多加一个要求，即等式右边的数不能相同，比如对于4来说，后面3种划分方式是不符合要求的，现在给定一个N，问有多少种划分方法，答案 mod 19901014

Input
第一行有个整数T，代表数据组数（T <= 10000）

接下来有TT行，每行有个整数NN (1 ≤ N ≤ 1000)

Output
输出T行，每行输出一个整数，代表N的划分数 mod 19901014

Sample 1
Input
```
3
4
5
10
```
Output
```
2
3
10
```

## 题解

### 算法一 类01背包`计数类dp`
* 错误的划分方法 dp[i][j] 表示整数i的划分的若干整数之和等于j
 dp[i][j] = dp[i-1][j] + dp[i][j-i]， 这样子的求出来显然是会有相同数在方案里。
正确的划分方法 dp[i][j] 表示整数i划分的若干整数中最大值不超过j
 **i >= j时候**   dp[i][j] = dp[i-j][j-1] + dp[i][j-1] (当前划分最大值选j， 当下划分最大值不选j)

但是dp的做法最后的时间复杂度为 1000 * 1000 * 10000 (n * n * T)

### 算法二 生成函数
**普通的生成函数**定义了对于有限数列{ai},定义其生成函数为∑ ai * x^i
举个例子对于数列为{2,4,6}的生成函数为 2 + 4x + 6x^2

在本题我们要求出来一个整数划分成不同的正整数，也就是说一个整数n划分，可以从1～n中每个数做选或不选即0/1，所以数列为{0,1,0,1...},每个数的数值作为次数
生成函数为：
g(x) = (1 + x)（1 + x^2)...(1 + x^n)
最后展开生成函数就知道划分成整数k的方案数就为生成函数k次项的系数

[生成函数](https://zhuanlan.zhihu.com/p/106522599)

### CODE
#### 计数类dp
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
const ll mod=19901014;
const int INF = 0x3f3f3f3f;
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head
const int N = 1010;

int dp[N][N];  // dp[i][j]表示 整数i划分成若干正整数最大值不超过j的情况下合法划分方案数
int T, n;

int main()
{
    cin >> T;

    // init 多组测试数据需要初始化
    memset(dp, 0, sizeof dp);
    dp[0][0] = 1;
    
    for(int i = 0; i < N; i ++)
        for(int j = 1; j < N; j ++)
        {
            // 当前的整数i划分最大值不超过j，情况可以分成两类严格不超过j， 非严格不超过j（也就是选j和不选j的情况）
            if(j <= i) dp[i][j] = (dp[i][j-1] + dp[i-j][j-1]) % mod;
            else dp[i][j] = dp[i][i];
        }        

    while(T --)
    {
        cin >> n;

        cout << dp[n][n] << endl;
    }

    return 0;
}
```

#### 生成函数
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
const ll mod=19901014;
const int INF = 0x3f3f3f3f;
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head
const int N = 1010;

int f[N];   // f[k]:  表示k次项的系数 
int a[N], b[N]; // 在多项式乘法的时候每次都是两个括号进行多项式乘法，a[i]：表示次数为i的系数

void Poly(int k)
{
    memset(f,0,sizeof f);
    for(int i = 0; i < N; i ++)
    {
        for(int j = 0; j < N - i; j += k)
            f[i + j] += (ll)(a[i] * b[j]) % mod;    // 可能在乘法这里overflow所以先强制类型转化
    }
}

int main()
{
    int T;
    cin >> T;

    // 预处理
    memset(a,0,sizeof a);
    a[0] = 1, a[1] = 1;     // 第一个括号(1 + x)
    for(int i = 2; i < N; i ++)
    {
        memset(b, 0, sizeof b);
        b[0] = 1, b[i] = 1;
        Poly(i);
        memcpy(a,f,sizeof f);
    }

    int n;
    while(T --)
    {
        cin >> n;
        cout << f[n] << endl;
    }
    
    return 0;
}
```