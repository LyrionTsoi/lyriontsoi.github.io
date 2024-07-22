---
title: AcWing 190. 字串变换
date: 2022-10-23 19:11:48
tags:
- 双向bfs
categories:
- AcWing
- 搜索
- 双向广搜
---

## [AcWing 190. 字串变换](https://www.acwing.com/problem/content/192/)
已知有两个字串 A, B 及一组字串变换的规则（至多 6 个规则）:

A1→B1
A2→B2
…

规则的含义为：在 A 中的子串 A1 可以变换为 B1、A2 可以变换为 B2…。

例如：A＝abcd B＝xyz

变换规则为：

abc → xu ud → y y → yz

则此时，A 可以经过一系列的变换变为 B，其变换的过程为：

abcd → xud → xy → xyz

共进行了三次变换，使得 A 变换为 B。

输入格式
输入格式如下：

A B
A1 B1
A2 B2
… …

第一行是两个给定的字符串 A 和 B。

接下来若干行，每行描述一组字串变换的规则。

所有字符串长度的上限为 20。

输出格式
若在 10 步（包含 10 步）以内能将 A 变换为 B ，则输出最少的变换步数；否则输出 NO ANSWER!。

输入样例：
```
abcd xyz
abc xu
ud y
y yz
```
输出样例：
```
3
```

### 题解

双向bfs是单向bfs的一种优化，所谓双向就是从源点和终点开始搜索，在中间状态会相遇这时搜索出来的最优状态的就是我们所搜的状态，在一般情下的搜索空间会少不少。
双向bfs主要用于的状态数目较大，单向bfs会T的情况。对于状态数目不大的情况，双向bfs优化空间比较小。

这里还涉及到双路bfs问题

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
const int N = 6;

int n;
string A, B; // 原始字符串和目标字符串
string a[N], b[N]; // 规则

int extend(queue<string> &q, unordered_map<string,int> &da, unordered_map<string,int> &db, string a[N], string b[N])
{
    // 双向bfs需要一整层的进行拓展
    // 这里的拓展是拓展同当前队列队首元素同一层的元素，所以要用d进行判别
    int d = da[q.front()];
    while(q.size() && da[q.front()] == d)
    {
        auto t = q.front();
        q.pop();
        // 找当前元素有没有符合规矩的
        for(int i = 0; i < n; i ++)
            for(int j = 0; j < t.size(); j ++)
            {
                if(t.substr(j,a[i].size()) == a[i])
                {
                    // 把中间a[i]串拿出来进行替换
                    string r = t.substr(0,j) + b[i] + t.substr(j + a[i].size());
                    if(db.count(r)) return da[t] + db[r] + 1;
                    if(da.count(r)) continue;
                    da[r] = da[t] + 1;
                    q.push(r);
                }
            }
    }
    // 没有相遇的情况，为了在bfs下再拓展所以这里返回值>10
    return 11;
}

int bfs()
{
    if (A == B) return 0;
    queue<string> qa,qb; // 两路bfs
    unordered_map<string, int> da, db; // 记录的是a队列内部的数据步长

    // 分别以两个端点作为起点
    // 两路bfs
    qa.push(A), qb.push(B);
    da[A] = db[B] = 0;

    // 这里记步数作为提前剪枝作用
    int step = 0;
    // 如果有任何一个队列为空表示其搜索完所有的状态这时候如果都找不到我们的目标状态那么说明不存在
    while(qa.size() && qb.size())
    {
        int t;
        // 这里比较随意，这里拓展我们就按那个队列比较短就谁先开始拓展
        if(qa.size() < qb.size()) t = extend(qa, da, db, a, b);
        else t = extend(qb, db, da, b, a);  // 反向拓展要记得b往a方向

        if(t <= 10) return t;
        if(++ step == 10) return -1;
    }
    return -1;
}

int main()
{
    cin >> A >> B;

    while(cin >> a[n] >> b[n]) n ++;

    int t = bfs();
    if (t == -1) puts("NO ANSWER!");
    else cout << t << endl;
    return 0;
}
```