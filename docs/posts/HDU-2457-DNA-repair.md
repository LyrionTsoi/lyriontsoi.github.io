---
title: HDU - 2457 DNA repair
date: 2022-08-08 19:21:43
tags:
- 状态机模型
- 动态规划
- ac自动机
categories:
- HDU
- 动态规划
- 状态机模型
---

## [HDU - 2457 DNA repair](https://vjudge.net/problem/HDU-2457#author=0)
生物学家终于发明了修复DNA的技术，能够将包含各种遗传疾病的DNA片段进行修复。

为了简单起见，DNA看作是一个由’A’, ‘G’ , ‘C’ , ‘T’构成的字符串。

修复技术就是通过改变字符串中的一些字符，从而消除字符串中包含的致病片段。

例如，我们可以通过改变两个字符，将DNA片段”AAGCAG”变为”AGGCAC”，从而使得DNA片段中不再包含致病片段”AAG”，”AGC”，”CAG”，以达到修复该DNA片段的目的。

需注意，被修复的DNA片段中，仍然只能包含字符’A’, ‘G’ , ‘C’ , ‘T’。

请你帮助生物学家修复给定的DNA片段，并且修复过程中改变的字符数量要尽可能的少。

输入格式
输入包含多组测试数据。

每组数据第一行包含整数N，表示致病DNA片段的数量。

接下来N行，每行包含一个长度不超过20的非空字符串，字符串中仅包含字符’A’, ‘G’ , ‘C’ , ‘T’，用以表示致病DNA片段。

再一行，包含一个长度不超过1000的非空字符串，字符串中仅包含字符’A’, ‘G’ , ‘C’ , ‘T’，用以表示待修复DNA片段。

最后一组测试数据后面跟一行，包含一个0，表示输入结束。

输出格式
每组数据输出一个结果，每个结果占一行。

输入形如”Case x: y”，其中x为测试数据编号（从1开始），y为修复过程中所需改变的字符数量的最小值，如果无法修复给定DNA片段，则y为”-1”。

数据范围
1≤N≤50
输入样例：
```
2
AAA
AAG
AAAG    
2
A
TG
TGAATG
4
A
G
C
T
AGT
0
```
输出样例：
```
Case 1: 1
Case 2: 4
Case 3: -1
```

### 题解
根据题目我们知道本题要求我们一条DNA链上对非致癌链进行修改的最小值

对于最优解问题或者求方案数问题的都可以使用动态规划来解决，本题又涉及到多字符串的匹配，所以还需要的ac自动机来维护。

动态规划集合划分
集合：dp[i][j]表示的以字符串i结尾的对应的自动机状态事j
属性：操作数最小
状态转移： f[i+1,trans[j,c]] = min(f[i][j], f[i,trans[j][c]])

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
const int N = 1010;

int n;
char str[N];
int tr[N][4], idx;
int fail[N], dar[N], q[N];
int dp[N][N];

int get(char x)
{
    if(x == 'A') return 0;
    if(x == 'G') return 1;
    if(x == 'C') return 2;
    return 3;
}

void insert()
{
    int p = 0;
    for(int i = 0; str[i]; i ++)
    {
        int t = get(str[i]);
        if(!tr[p][t])
            tr[p][t] = ++ idx;
        p = tr[p][t];
    }
    dar[p] = 1;
}

void build()
{
    int hh = 0, tt = -1;
    for(int i = 0; i < 4; i ++)
        if(tr[0][i])
            q[++ tt] = tr[0][i];    // 把所有的非空状态先入队

    while(hh <= tt)
    {
        int t = q[hh ++];   // 当前层的节点

        for(int i = 0; i < 4; i ++)
        {
            int p = tr[t][i];
            if(p)
            {
                fail[p] = tr[fail[t]][i];
                q[++ tt] = p;
                // 如果他的某一个前缀子串的最大相同前后缀（fail[p])出现了另一个模式串匹配成功的状态
                // 说明当前这个状态就是匹配成功的状态
                dar[p] |= dar[fail[p]]; 
            }
            else tr[t][i] = tr[fail[t]][i];
        }
    }
}

int main()
{
    int T = 1;
    while(cin >> n, n)
    {
        // init 
        memset(tr,0,sizeof tr);
        memset(dar, 0, sizeof dar);
        memset(fail, 0, sizeof fail);
        memset(dp, 0x3f, sizeof dp);
        idx = 0;

        // build the trie tree
        for(int i = 0; i < n; i ++)
        {
            scanf("%s",str);
            insert();
        }

        // build the ac automaton
        build(); 

        scanf("%s",str + 1);
        int m = strlen(str + 1);

        // dp
        dp[0][0] = 0;
        for(int i = 0; i < m; i ++)
            for(int j = 0; j <= idx; j ++)
                for(int k = 0; k < 4; k ++)
                {
                    // 表示当前节点的花费，因为不一定在以（i+1)结尾的字符能和模式串匹配
                    //如果是前缀匹配那么转移就不需要操作（因为前面一定已经加上了贡献）
                    int t = get(str[i + 1]) != k;   
                    int p = tr[j][k];   // tr[j][k]表示当前状态通过k转移过来
                    // 朴素的版本就是一遍遍检查是否有当前字符串的子串能与模式串匹配的，能匹配就更新，不能匹配就不更新
                    if(!dar[p]) 
                        dp[i + 1][p] = min(dp[i+1][p], dp[i][j] + t);
                }
        int res = INF;
        for(int i = 0; i <= idx; i ++)
            res = min(res, dp[m][i]);
        if(res == INF) res = -1;
        printf("Case %d: %d\n", T ++, res);

    }
    return 0;
}
```