---
title: AcWing 3729. 改变数组元素
date: 2023-02-23 20:19:24
tags:
- Acwing
- 差分
Categories:
- Acwing
- 蓝桥杯
- 差分
---

# [题意](https://www.acwing.com/problem/content/3732/)

给定一个空数组 V 和一个整数数组 a1,a2,…,an。

现在要对数组 V 进行 n 次操作。

第 i 次操作的具体流程如下：

1. 从数组 V 尾部插入整数 0。
2. 将位于数组 V 末尾的 ai 个元素都变为 1（已经是 1 的不予理会）。

注意：

- ai 可能为 0，即不做任何改变。
- ai 可能大于目前数组 V 所包含的元素个数，此时视为将数组内所有元素变为 1。

请你输出所有操作完成后的数组 V。

**输入格式**

第一行包含整数 T，表示共有 T 组测试数据。

每组数据第一行包含整数 n。

第二行包含 n 个整数 a1,a2,…,an。

**输出格式**

每组数据输出一行结果，表示所有操作完成后的数组 V，数组内元素之间用空格隔开。

**数据范围**

1≤T≤20000,
1≤n≤2×105,
0≤ai≤n,
保证一个测试点内所有 n 的和不超过 2×105。

**输入样例**：

```
3
6
0 3 0 0 1 3
10
0 0 0 1 0 5 0 0 0 2
3
0 0 0
```

**输出样例**：

```
1 1 0 1 1 1
0 1 1 1 1 1 0 0 1 1
0 0 0
```

# 题解

**sample**

原数组`[....]`; 操作数组` [0,3,0,0,1,3]`

实际的操作序列为：`  [0]->[0]->[0,0]->[1,1]->[1,1,0]->[1,1,0]->[1,1,0,0]->[1,1,0,0]->[1,1,0,0,0]->[1,1,0,0,1]->[1,1,0,0,1,0]->[1,1,0,1,1,1]`

由于题目已经提前告诉我们所有的操作，所以我们就可以先把空数组的长度设置为操作的操作数目，并且每一个位都置为0

要改变从末尾往前数ai位数为0，可以变换成更改区间[ai-x+1,ai]所有数值都置为1

可以开一个数组记录每一位置的操作，每次操作一次操作数都加一，只要操作数≥1那么这个位置一定为1

**对区间操作的步骤叫做差分**

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
const int N = 200010;

int cnt[N], op[N];
int T;

int main()
{
    // sample
    // []; [0,3,0,0,1,3]
    //[0]->[0]->[0,0]->[1,1]->[1,1,0]->[1,1,0]->[1,1,0,0]->[1,1,0,0]->[1,1,0,0,0]->[1,1,0,0,1]->[1,1,0,0,1,0]->[1,1,0,1,1,1]
    
    // 由于题目已经提前告诉我们所有的操作，所以我们就可以先把空数组的长度设置为操作的操作数目，并且每一个位都置为0
    // 要改变从末尾往前数ai位数为0，可以变换成更改区间[ai-x+1,ai]所有数值都置为1
    // 可以开一个数组记录每一位置的操作，每次操作一次操作数都加一，只要操作数≥1那么这个位置一定为1
    // 对区间操作的步骤叫做差分

    cin >> T;
    while(T --)
    {
        int n; // 数组的长度
        cin >> n;
        // 初始化数组
        memset(cnt,0,(n+1)*4);

        // input
        for(int i = 1; i <= n; i ++)
        {
            scanf("%d",&op[i]);
            op[i] = min(i,op[i]); // 这里要注意的！！！不是与n取min，而是与op[i]取最小值
        }

        // 遍历操作数组
        for(int i = 1; i <= n; i ++)
        {
            int x = op[i];
            // 差分数组
            cnt[i - x + 1] ++, cnt[i + 1] --;
        }

        // 构造出原数组
        for(int i = 1; i <= n; i ++)
            cnt[i] += cnt[i-1];

        for(int i = 1; i <= n; i ++)
            printf("%d ",!!cnt[i]); // 实现了非零输出1，0的话输出0

        puts("");
    }


    return 0;
}
```

