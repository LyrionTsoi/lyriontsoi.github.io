---
title: POJ - 3342 Party at Hali-Bula
date: 2022-08-03 17:37:24
tags:
- 最大独立集
- 树形dp
categories:
- POJ
- 树形dp
- 最大独立集唯一性
---

## [POJ - 3342 Party at Hali-Bula](https://vjudge.net/problem/POJ-3342)
Dear Contestant,

I'm going to have a party at my villa at Hali-Bula to celebrate my retirement from BCM. I wish I could invite all my co-workers, but imagine how an employee can enjoy a party when he finds his boss among the guests! So, I decide not to invite both an employee and his/her boss. The organizational hierarchy at BCM is such that nobody has more than one boss, and there is one and only one employee with no boss at all (the Big Boss)! Can I ask you to please write a program to determine the maximum number of guests so that no employee is invited when his/her boss is invited too? I've attached the list of employees and the organizational hierarchy of BCM.

Best,
--Brian Bennett

P.S. I would be very grateful if your program can indicate whether the list of people is uniquely determined if I choose to invite the maximum number of guests with that condition.

Input
The input consists of multiple test cases. Each test case is started with a line containing an integer n (1 ≤ n ≤ 200), the number of BCM employees. The next line contains the name of the Big Boss only. Each of the following n-1 lines contains the name of an employee together with the name of his/her boss. All names are strings of at least one and at most 100 letters and are separated by blanks. The last line of each test case contains a single 0.

Output
For each test case, write a single line containing a number indicating the maximum number of guests that can be invited according to the required condition, and a word Yes or No, depending on whether the list of guests is unique in that case.

Sample
Input
```
6
Jason
Jack Jason
Joe Jack
Jill Jason
John Jack
Jim Jill
2
Ming
Cho Ming
0
```
Output
```
4 Yes
1 No
```

### 题解
**最大独立集唯一性**
状态转移方程：
// 不选i
dp[i][0] += max(dp[j][1],dp[j][0]);
// 选
dp[i][1] += dp[j][0];


前提： 假设是以i为根的树， j是i的子节点
1. 显然如果以i位根的树dp[i][0] == dp[i][1]这种情况下，最大独立集不唯一。但是要注意不唯一情况下是有可能出现dp[i][0] != dp[i][1]
2. 如果dp[j][0] == dp[j][1],并且如果max(dp[i][0],dp[i][1]) == dp[i][0](也就是说最大独立集通过子树j的最大独立集转移过来所以)，这个情况下最后的最大独立集一定不唯一
   ```
   if(dp[j][0] == dp[j][1])
        judge[i][0] = true;
    ```
3. 如果子节点不选的时候不唯一的情况下，那么当前节点选上了那么整个树也一定不唯一
    ```
    if(judge[j][0])
        judge[i][1] = true;
    ```

**本题要注意的是，在寻找唯一性不能对仅对数组dp检查，因为dp内保存的仅是所有方案中有一中最优方案**

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
const int N = 500;

int h[N], e[N], ne[N],idx;
int dp[N][2];
bool judge[N][2];
int n,res;
map<string, int> peo;

void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

void dfs(int u)
{
    if(u == -1) return;

    dp[u][0] = 0;
    dp[u][1] = 1;
    judge[u][0] = judge[u][1] = true;
    for(int i = h[u]; ~i; i = ne[i])
    {
        int j = e[i];
        dfs(j);

        // 判断唯一性（如果子树都不唯一了那么最后最大独立集一定也不唯一）
        // 更新judge数组
        // 当前节点不选的情况下，如果子节点最大独立集不唯一的话，那么总方案一定不唯一

        if(dp[j][0] == dp[j][1])
            judge[u][0] = false;
        // 当子节点不选的情况下最大独立集不唯一的，总的最大独立集也不唯一
        if(!judge[j][0])
            judge[u][1] = false;

        // 更新dp数组
        dp[u][0] += max(dp[j][0], dp[j][1]);
        dp[u][1] += dp[j][0];
    }
}

int main()
{
    while(cin >> n, n)
    {
        // init()
        peo.clear();
        res = 0,idx = 0;
        memset(h,-1,sizeof h);

        // big boss(root)
        string a,b;
        int num = 1;
        cin >> a;
        peo[a] = num ++;

        for(int i = 0; i < n - 1; i ++)
        {
            cin >> a >> b;
            if(!peo[a])
                peo[a] = num ++;
            if(!peo[b])
                peo[b] = num ++;

            add(peo[b],peo[a]);
        }   

        dfs(1);

        if(dp[1][0] > dp[1][1] && judge[1][0])
            printf("%d Yes\n",dp[1][0]);
        else if(dp[1][0] < dp[1][1] && judge[1][1])
            printf("%d Yes\n", dp[1][1]);
        else printf("%d No\n",max(dp[1][0],dp[1][1]));
        
    }

    return 0;
}
```