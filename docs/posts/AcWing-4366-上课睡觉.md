---
title: AcWing 4366. 上课睡觉
date: 2022-12-30 16:05:08
tags:
- 枚举
- usaco
categories:
- AcWing
- usaco
- 枚举
---

# AcWing 4366. 上课睡觉

## 题目

有 N 堆石子，每堆的石子数量分别为 a1,a2,…,aN。

你可以对石子堆进行合并操作，将两个相邻的石子堆合并为一个石子堆，例如，如果 a=[1,2,3,4,5]，合并第 2,3 堆石子，则石子堆集合变为 a=[1,5,4,5]。

我们希望通过尽可能少的操作，使得石子堆集合中的每堆石子的数量都相同。

请你输出所需的最少操作次数。

本题一定有解，因为可以将所有石子堆合并为一堆。

**输入格式**

第一行包含整数 T，表示共有 T 组测试数据。

每组数据第一行包含整数 N。

第二行包含 NN 个整数 a1,a2,…,aN。

**输出格式**

每组数据输出一行结果。

**数据范围**

1≤T≤10,
1≤N≤ $10^5$
0≤ai≤ $10^6$,
$\sum_{i=1}^n a_i \leq 10^6$ ,
每个输入所有 N 之和不超过 $10^5$。

**输入样例**：

```
3
6
1 2 3 1 1 1
3
2 2 3
5
0 0 0 0 0
```

**输出样例**：

```
3
2
0
```

**样例解释**

第一组数据，只需要用 33 个操作来完成：

```
   1 2 3 1 1 1
-> 3 3 1 1 1
-> 3 3 2 1
-> 3 3 3
```

第二组数据，只需要用 22 个操作来完成：

```
   2 2 3
-> 2 5
-> 7
```

第三组数据，我们什么都不需要做。

## 题解

 **如果可以通过一定的合并操作使得每一个数都相同那么一定有:**
$$
\sum a_i \mod {cnt} = 0
$$


* 因为题目要求的是所有石子合并都是通过相邻的石子进行合并的所以可以看做一段数进行多次划分，最后划分成n组（且 $\sum a_i \mod {cnt} = 0 or \sum a_i / cnt = n$），每一组的数值之和都等于cnt

  ![](https://124newblog-1309411887.cos.ap-nanjing.myqcloud.com/images/202212301754470.png)

**划分过程**

* 前提：因为所有的数值都为正数
* 从头开始遍历原数组
  * 如果加上当前这个数 $>cnt$,那么意味着这一连续的一段数无法凑出来cnt
  * 如果加上当前这个数 $=cnt$，那么意味着这一连续的一段数已经凑出来cnt，如果相邻的下一个数为0，那么把这个0划分在左边一组，因为0并不影响划分。

* 边界情况考虑
  * 是否需要考虑最后一个组的划分不够这个情况？？
  * 不需要考虑边界不够cnt，因为在主函数判断sum%i可以被i划分为整数倍的数组，所以只要前面满足了，最后一个序列一定也会满足
  * 是否需要考虑最后一组cnt == 0，因为在迭代过程中一旦==cnt,cnt就会清空,所以最后一组无论数值之和为0还是为cnt最后的表现都为0？？
  * 其实是不用的，因为最后一组为cnt表示合法方案
  * 最后一组为0，那么就可以把这一组归在相邻的左边那一组上

**时间复杂度计算**

* 每一个数的约数大约为 $\log{n}$级别，证明如下：
  * $1～n约数之和= \frac{n}{1} + \frac{n}{2} + ... + \frac{n}{n} = n\log{n}$,那么每个数的约数个数 averge = $\log{n}$

所以根据上面就知道的了本题的时间复杂度大概在 $n\log{n}$

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
const int N = 100010;

int arr[N];
int T, n, sum;

bool check(int h)
{
    // 遍历原始堆，划分每一个段是否能构成 cnt = sum/h 
    int cnt = 0;
    for(int i = 0; i < n; i ++)
    {
        cnt += arr[i];
        if(cnt > sum / h) return false;
        if(cnt == sum / h) cnt = 0;
    }

    // 边界情况
    // 是否需要考虑最后一个组的划分不够这个情况
    // 不需要考虑边界不够cnt，因为在主函数判断sum%i可以被i划分为整数倍的数组，所以只要前面满足了，最后一个序列一定也会满足
    // 是否需要考虑最后一组cnt == 0，因为在迭代过程中一旦==cnt,cnt就会清空,所以最后一组无论数值之和为0还是为cnt最后的表现都为0
    // 那么这个情况下是否需要判断呢？？
    // 其实是不用的，因为最后一组为cnt表示合法方案
    // 最后一组为0，那么就可以把这一组归在相邻的左边那一组上面
    return true;
}

int main()
{
    cin >> T;

    while(T --)
    {
        cin >> n;
        sum = 0;
        for(int i = 0; i < n; i ++)
        {
            scanf("%d",&arr[i]);
            sum += arr[i];
        }

        // 枚举最后会有多少个堆
        // 这里的最后操作数 = n - 当前枚举到的合法堆数
        for(int i = n; i; i --)
        {
            if(!(sum%i) && check(i))
            {
                printf("%d\n", n - i);
                break;
            }
        }
    }

    return 0;
}
```

