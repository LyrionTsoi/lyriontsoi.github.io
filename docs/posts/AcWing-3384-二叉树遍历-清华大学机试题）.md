---
title: AcWing 3384. 二叉树遍历(清华大学机试题）
date: 2023-05-09 16:54:24
tags:
---

## [题意](https://www.acwing.com/problem/content/description/3387/)

编写一个程序，读入用户输入的一串先序遍历字符串，根据此字符串建立一个二叉树（以指针方式存储）。

例如如下的先序遍历字符串： `abc##de#g##f###` 其中 `#` 表示的是空格，空格字符代表空树。

建立起此二叉树以后，再对二叉树进行中序遍历，输出遍历结果。

#### 输入格式

共一行，包含一个字符串，表示先序遍历字符串。

#### 输出格式

共一行，输出将输入字符串建立二叉树后中序遍历的序列，字符之间用空格隔开。

注意，输出中不用包含 `#`。

#### 数据范围

输入字符串长度不超过 100100，且只包含小写字母和 `#`。

#### 输入样例：

```
abc##de#g##f###
```

#### 输出样例：

```
c b e g d f a
```

## 题解

⚠️这是第一遍不是很会题

主要结合目的来做题，只需要输出中序遍历的结果。题目给出来先序遍历的结果，又知道先序遍历是我们平时的深度优先遍历，所以按照左中右dfs的方式走就行

## CODE

```c++
#include <bits/stdc++.h>
using namespace std;
#define PI 3.14159265359
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
string in;
int p;

void dfs(){
    // p是按照先序遍历的形式来走的
    // 输出只需要管根节点就行了，记录根节点编号即可
    if(in[p] == '#')
    {
        p ++;
        return; // 如果一个点为空，那么这个子树就不需要再遍历下去了
    }

    // 这里我们不需要建边，目的在于输出中序遍历的结果
    int root = p ++;
    dfs(); // 遍历左子树
    cout << in[root] << ' '; // 遍历完左子树之后就输出当前节点的编号
    dfs(); // 遍历右子树
}

int main()
{   
    cin >> in;

    // 先序遍历也就是我们平时所说的dfs深度优先遍历
    dfs();

    return 0;
}
```

