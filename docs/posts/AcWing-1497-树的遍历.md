---
title: AcWing 1497. 树的遍历
date: 2023-03-16 21:26:27
tags:
- 二叉树的遍历
Categories:
- AcWing 
- 蓝桥杯
- 递归
---

# [题意](https://www.acwing.com/problem/content/1499/)

一个二叉树，树中每个节点的权值互不相同。

现在给出它的后序遍历和中序遍历，请你输出它的层序遍历。

**输入格式**

第一行包含整数 N，表示二叉树的节点数。

第二行包含 N 个整数，表示二叉树的后序遍历。

第三行包含 N 个整数，表示二叉树的中序遍历。

**输出格式**

输出一行 N个整数，表示二叉树的层序遍历。

**数据范围**

1≤N≤30
官方并未给出各节点权值的取值范围，为方便起见，在本网站范围取为 1∼N。

**输入样例：**

```
7
2 3 1 5 7 6 4
1 2 3 4 5 6 7
```

**输出样例：**

```
4 1 6 3 5 7 2
```

# 题解

1. **先序遍历**

   采用的是根左右的遍历方式。每次的遍历的次序无非就是先遍历左子树，遍历到最后的左子树，输出叶子结点的序号，然后再输出根节点序号，再遍历右子树的顺序。

2. **中序遍历**

   采用的是左右根的遍历方式

3. **后序遍历**

   左右根

4. 层序遍历

   BFS

**先序、中序、后序无非就是基于的DFS的改变**

这里使用的是通过后序和中序遍历构建子树，下面是具体的过程。

![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202303162144747.png)

1. 通过后序遍历的顺序找到的根节点
2. 在中序遍历的比对找到根节点的在中序的遍历的顺序，继而划分出左右子树
3. 假设左中找到的了有A个节点，那么意味着在后序遍历的前A个节点都是的左子树，同理右子树的划分。



⚠️注意，这里bfs代与我们平常使用的数组存图不完全一样，所以这里的代码是非常值得参考的。

# CODE

```c++
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

// a是先序 b是后序 p是 b[i]到i的映射
int a[N], b[N], p[N];
int l[N], r[N]; // l[root] 表示的以root为根节点的子树 同理r[]
int n;

int build(int al, int ar, int bl, int br)
{
    if(al > ar) return 0;
    int val = a[ar]; // 当前子树的最后一个节点就是的根节点
    int k = p[val]; // 寻找根节点在中序遍历的位置
    l[val] = build(al, al + k - 1 - bl, bl, k - 1);
    r[val] = build(al + k - bl, ar - 1, k + 1, br);
    return val;
}

void bfs()
{
    queue<int> q;
    q.push(a[n-1]); // 整颗树的根节点
    while(q.size())
    {
        int t = q.front();
        cout << t << ' ';
        q.pop();
        // 表示如何当前根节点t如果还有左子树/右子树
        if(l[t]) q.push(l[t]);
        if(r[t]) q.push(r[t]);
    }
}

int main()
{
    cin >> n;
    for(int i = 0; i < n; i ++) cin >> a[i];
    for(int j = 0; j < n; j ++) 
    {
        cin >> b[j];
        p[b[j]] = j;
    }

    // for(auto p : a) cout << p << ' ';
    // puts("");

    build(0,n-1,0,n-1);

    bfs();
    return 0;
}
```

