---
title: CodeForces - 1699B Almost Ternary Matrix
date: 2022-07-13 19:32:58
tags: 
- 思维题
- 构造
categories : 
- CodeForces
- 思维题
---

## [CF-1699B](https://codeforces.com/problemset/problem/1699/B)

You are given two even integers 𝑛 and 𝑚. Your task is to find any binary matrix 𝑎 with 𝑛 rows and 𝑚 columns where every cell (𝑖,𝑗) has exactly two neighbours with a different value than 𝑎𝑖,𝑗.

Two cells in the matrix are considered neighbours if and only if they share a side. More formally, the neighbours of cell (𝑥,𝑦) are: (𝑥−1,𝑦), (𝑥,𝑦+1), (𝑥+1,𝑦) and (𝑥,𝑦−1).

It can be proven that under the given constraints, an answer always exists.

Input
Each test contains multiple test cases. The first line of input contains a single integer 𝑡 (1≤𝑡≤100) — the number of test cases. The following lines contain the descriptions of the test cases.

The only line of each test case contains two even integers 𝑛 and 𝑚 (2≤𝑛,𝑚≤50) — the height and width of the binary matrix, respectively.

Output
For each test case, print 𝑛 lines, each of which contains 𝑚 numbers, equal to 0 or 1 — any binary matrix which satisfies the constraints described in the statement.

It can be proven that under the given constraints, an answer always exists.

Example
input
```
3
2 4
2 2
4 4
```
output
```
1 0 0 1
0 1 1 0
1 0
0 1
1 0 1 0
0 0 1 1
1 1 0 0
0 1 0 1
```

### 题解
 ![]([https://](https://codeforces.com/predownloaded/75/c4/75c46e19cc3cf6f890139b0e74774c3a6fc387db.png)) 
我们需要构造上图的2x2矩形，我们可以看出宏观上来看都是每个2个连续出现两次黑块/白块，左上角的坐标记作（1，1）我们可以得到一个公式 (i%4 <=1 != j%4<= 1) / (i%4 <=1 == j%4<= 1)时是黑块

### CODE
```c
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

#define LL long long
#define pii pair<int,int>x


using namespace std;

const int INF = 0x3f3f3f3f;

int T;
int n,m;

int main()
{
    cin >> T;
    while(T --)
    {
        cin >> n >> m;

        for(int i = 1; i <= n; i ++)
            for(int j = 1; j <= m; j ++)
                cout << ((i % 4 <= 1) == (j % 4 <= 1)) << " \n"[j==m];
    }
    return 0;
}
```