---
title: UVA - 11795 Mega Man's Mission
date: 2022-08-05 20:59:47
tags:
- 动态规划
- 状态压缩
categories:
- UVA
- 动态规划
- 状态压缩
---

## [UVA - 11795 Mega Man's Mission](https://vjudge.net/problem/UVA-11795)
Mega Man is off to save the world again. His objective is to kill the Robots created by Dr. Wily whose motive is to
conquer the world. In each mission, he will try to destroy a particular Robot. Initially, Mega Man is equipped with a weapon, called the “Mega Buster” which can be used to destroy the Robots. Unfortunately, it may happen that his weapon is not capable of taking down every Robot. However,to his fortune, he is capable of using the weapons from Robots which he has completely destroyed and these weapons maybe able to take down Robots which he otherwise cannot with his own weapon. Note that, each of these enemy Robots carry exactly one weapon themselves for fighting Mega Man. He is able to take down the Robots in any order as long as he has at least one weapon capable of destroying the Robot at a particular mission. In this problem, given the information about the Robots and their weapons, you will have to determine the number of ways Mega Man can complete his objective of destroying all the Robots.

**Input**
Input starts with an integer T (T ≤ 50), the number of test cases.
Each test case starts with an integer N (1 ≤ N ≤ 16). Here N denotes the number of Robots to be
destroyed (each Robot is numbered from 1 to N). This line is followed by N + 1 lines, each containing
N characters. Each character will either be ‘1’ or ‘0’. These lines represent a (N + 1) × N matrix. The
rows are numbered from 0 to N while the columns are numbered from 1 to N. Row 0 represents the
information about the “Mega Buster”. The j-th character of Row 0 will be ‘1’ if the “Mega Buster”
can destroy the j-th Robot. For the remaining N rows, the j-th character of i-th row will be ‘1’ if
the weapon of i-th Robot can destroy the j-th Robot. Note that, a Robot’s weapon could be used to
destroy the Robot itself, but this will have no impact as the Robot must be destroyed anyway for its
weapon to be acquired.

**Output**
For each case of input, there will be one line of output. It will first contain the case number followed
by the number of ways Mega Man can complete his objective. Look at the sample output for exact
format.

Input
```
3
1
1
1
2
11
01
10
3
110
011
100
000
```
Output
```
Case 1: 1
Case 2: 2
Case 3: 3
```

### 题解
题目要我们求杀光所有机器人的总方案数
我们有一个初始武器可以kill某几个机器人，然后用我们杀死的机器武器再kill别的机器人。

机器人的数目很少，所以可以考虑用二进制形式的状态压缩来枚举所有的状态

正常的状态压缩步骤
1. 预处理所有的合法状态
2. 预处理我们的当前的状态可以从什么状态转移过来
3. dp

**本题的预处理技巧值得关注**

### CODE
```C++
#include <vector>
#include <cstring>
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <stack>
#include <queue>
#include <deque>
#include <set>
#include <map>
#include<cmath>
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
#define int long long
typedef vector<int> VI;
typedef basic_string<int> BI;
typedef long long ll;
typedef pair<int,int> PII;
typedef double db;
const ll mod=1000000007;
const int INF = 0x3f3f3f3f;
ll gcd(ll a,ll b) { return b?gcd(b,a%b):a;}
// head
const int N = 17;

int dp[1 << N]; // dp[i]表示当前i状态下的所有方案
int head[1 << N];   // 预处理当前方案可以杀掉的机器人
int kill[1 << N];    // kill[i]表示的i号机器人可以kill的机器人
int n;

signed main()
{
    int T;
    cin >> T;
    int step = 1;
    while(T --)
    {
        // init()
        memset(dp,0,sizeof dp);
        memset(head,0,sizeof head);
        memset(kill,0,sizeof kill);

        cin >> n;
        // 预处理每一个机器人能kill的其他机器人
        for(int i = 0; i <= n; i ++)
        {
            string s;
            cin >> s;
            for(int j = 0; j < n; j ++)
            {
                if(s[j] == '1')
                    kill[i] |= (1 << j);
            }
        }

        // 预处理每一种状态下能kill的机器人
        for(int st = 0; st < (1 << n); st ++)
        {
            head[st] = kill[0];
            // 从a0～an-1
            for(int i = 0; i <= n - 1; i ++)
            {
                // 当前状态下这一位是1的话，1这个位置对应的机器人能kill的加到st能kill的里面
                if(st & (1 << i))
                {
                    head[st] |= kill[i + 1];
                }
            }
        }

        dp[0]= 1;  // 所有的机器人都不被杀掉只有一种方案什么都不选
        for(int st = 1; st < (1 << n); st ++)
        {
            // 从当前状态移除一个1
            for(int i = 0; i <= n-1; i ++)
            {
                if(st & (1 << i))
                {
                    // 移去一个1并判断边界
                    // 判断上一个状态是否可以转移过来当前状态(也就是判断上一个状态是否可以kill我们一去的机器人)
                    if(head[st^(1 << i)] & (1 << i))
                        dp[st] += dp[st^(1 << i)];
                }
            }
        }

        printf("Case %d: %lld\n",step ++, dp[(1 << n) - 1]);
    }
    
    return 0;
}
```