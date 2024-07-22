---
title: AcWing 167. 木棒
date: 2022-11-14 16:23:40
tags:
- 搜索
- 剪枝
categories:
- AcWing
- 搜索
- dfs剪枝
---

# [AcWing 167. 木棒](https://www.acwing.com/problem/content/description/169/)
乔治拿来一组等长的木棒，将它们随机地砍断，使得每一节木棍的长度都不超过 50 个长度单位。

然后他又想把这些木棍恢复到为裁截前的状态，但忘记了初始时有多少木棒以及木棒的初始长度。

请你设计一个程序，帮助乔治计算木棒的可能最小长度。

每一节木棍的长度都用大于零的整数表示。

输入格式
输入包含多组数据，每组数据包括两行。

第一行是一个不超过 64 的整数，表示砍断之后共有多少节木棍。

第二行是截断以后，所得到的各节木棍的长度。

在最后一组数据之后，是一个零。

输出格式
为每组数据，分别输出原始木棒的可能最小长度，每组数据占一行。

数据范围
数据保证每一节木棍的长度均不大于 50。

输入样例：
```
9
5 2 1 5 2 1 5 2 1
4
1 2 3 4
0
```
输出样例：
```
6
5
```

## 题解
在这里剪枝主要是有四种
1. 优化搜索顺序
   从大到小进行枚举，具体原因可以看[小猫爬山](AcWing-165-小猫爬山-模版.md)
2. 排除等效冗余
   1. 按照组合数方式进行枚举，这里的组合数方式主要是通过的对每个木棒进行编号处理，每次枚举从编号小到大进行枚举
   2. 如果当前木棒加到当前棒中，失败了。则直接略过后面所有和其长度相等的木棒
   3. 如果木棒的第一个枚举到最后失败了则一定失败
   4. 如果木棒的最后一根枚举到最后失败了则一定失败

**证明**

*2-2剪枝*

假设a = 10，b = 10，a放在某一根木棒内部的时候失败了，意味这我这根木棒应该放在新的木棒里面。因为木棒是按大小排号序的（即使不排序也不会影响我们的结论），如果这个木棒放在同一个位置上搜索出来一个合法方案。这样就意味这一个位置上的木棒应该是能和其相同大小的木棒进行交换的。这样就和我们的木棒a放在同一个地方搜索不出来的合法方案的情况矛盾了。

*2-3剪枝*

如果木棒放在第一个失败了，那么就意味其是否应该放入木棒中间或最后。但是如果可以放在中间或者最后的话，就意味在同一根木板内部是可以相互交换顺序的，所以矛盾。

可能通过上面的证明大家可能感觉有些奇怪，如果我用相同论证方式也可以论证出来同一根中间失败了，其就失败了，但是很明显这个结论绝对是错误的。那为什么同一个论证方式却失败了？？

我们可以想的时候，2-3的论证是无论木棒在哪一根大木棒当中，只要方案成立都是可以在木棒内部的首部进行交换顺序的，这时候他处于哪一根木棒的首部都是没有任何影响的。但是对于中间某一根木棒，他是否对与处于哪一根木棒的某一个位置都不会影响呢？？很明显并不是，他是很木棒内部的组合息息相关的。

*2-4剪枝*

这个可以在木棒内部转换成木棒的首部，然后通过2-3剪枝的证得，如果木棒处于最后一根木棒的时候失败了就可以提前剪枝了

**这里的提前剪枝是不用再去枚举当前木棒的之后的所有可能的组合方案了，所以剪枝的思想也要考虑的是当前木棒所有方案的前提下**

## CODE
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
const int N = 70;

int n;
int w[N];
int sum, length;    // sum 表示总长度， length 表示木棒的长度（我们要找出所有木棒中的最小长度）
bool st[N];

bool dfs(int u, int cur, int start)
{
    if(u * length == sum) return true; // 如果木棒的数目*每根木棒的长度等于总长度的时候就表示我们已经排出来一种可行性方案
    // 这里的dfs从0开始的原因是因为前面可能有些数并没有用上，所以在这里需要重头开始枚举
    if(cur == length) return dfs(u + 1, 0, 0); // 如果当前木棒已经达到我们要求的长度的话，就需要进行开辟一个新的木棒

    for(int i = start; i < n; i ++)
    {
        // 如果当前木棒在前面选择了就不选者，如果当前加上小木棒的值超出我们要求的木棒长度也进行剪枝
        if(st[i] || cur + w[i] > length) continue;

        // 选择当前小木棒作为组合方案
        st[i] = true;
        if(dfs(u,cur+w[i], i + 1)) return true;
        st[i] = false;

        // 当前一根是不合法的状态
        // 如果前这一根处于首部和尾部的状态的话
        if(!cur || cur + w[i] == length) return false;

        // 相同长度的如果不合法，那么其他的也不会合法
        int j = i;
        while(j < n && w[j] == w[i]) j ++;
        i = j - 1;
    }

    return false;
}

int main()
{
    while(cin >> n, n)
    {
        memset(st,0,sizeof st);
        sum = 0;
        for(int i = 0; i < n; i ++)
        {
            cin >> w[i];
            sum += w[i];
        }

        sort(w,w + n);
        reverse(w,w+n);
        for(length = 1; length <= sum; length ++)
            if(! (sum % length)  && dfs(1,0,0))
            {    
                cout << length << endl;
                break;
            }

    }

    return 0;
}
```