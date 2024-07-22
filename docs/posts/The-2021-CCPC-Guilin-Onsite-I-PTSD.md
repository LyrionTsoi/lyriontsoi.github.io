---
title: The 2021 CCPC Guilin Onsite I. PTSD
date: 2022-07-15 20:21:56
tags:
- CCPC
- 思维题
- 构造
- 代码能力
categories: 思维题
---

## [The 2021 CCPC Guilin Onsite I. PTSD](https://vjudge.net/problem/Gym-103409I#author=sunhaoyu)

有n个战士，编号为1~n，第i个战士的武力值为i，现在需要把n个战士分为若干组，每个战士都必须属于某个组，每个组都至少有一个战士，有一些战士会生一种怪病，就是当他所在的组有且仅有一名战士的武力值比他高时他就会难过qaq，然后现在你需要给这些战士分组，使得患病并难过的战士的武力值之和最大。
样例输入
有多组样例，第一行输入样例组数T，对于每一组样例：
第一行是一个整数 n [1,1e6],表明战士的数量

第二行是一个长度为n的01串，第i个位置为'0'表示第i个战士没有患病，第i个位置为'1'表示第i个战士患病，保证所有样例的n之和不超过1e6
 
样例输出
对于每一组样例，输出一行包含一个整数表示患病并难过的战士的武力值之和的最大值

Input
```
4
5
10101
8
11111111
4
1100
4
0110
```
Output
```
4
16
3
3
```
Note
对于第一组样例，一个有效的分组方法是 [1, 2], [3, 4], [5],它使得第一个战士和第三个战士难过， [1, 2], [3, 5], [4] 也可以

对于第二组样例，一个有效的分组方法是 [1, 2], [3, 4], [5, 6], [7, 8].
对于第三组样例，一个有效的分组方法是[1, 3], [2, 4].
对于第四组样例，一个有效的分组方法是[1, 2, 3, 4].

### 题解
等价于两两匹配。因为我们需要尽可能的组成更多的组，因为一组只能加一个权值，所以我们要组数越多那么组内的数据就要越小。
而且这里的匹配一定是大的数和大的数匹配，因为大的数和小的数匹配，这样最后的到匹配权值之和是<=我们大的数和大的数匹配的（因为权值是加小的数）1111101 
如果较小的为 PTSD 则对答案有贡献.
从大到小枚举每个数, 维护一个初值为 0 的计数器, 如果它是PTSD 且计数器大于 0, 说明有更大的数还未匹配, 那么统计进答案且计数器 −1. 否则计数器 +1. 可以发现这样答案是最优的. 复杂度 O(n)
**同时要注意这里的答案是有可能爆int的，1+...+1e6 = 1e12的级别**

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
#define pii pair<int,int>


using namespace std;

const int INF = 0x3f3f3f3f;

int main()
{
    int T;
    cin >> T;
    while(T --)
    {
        int n;
        string s;
        cin >> n >> s;
        s = ' ' + s;

        int cnt = 0;    // 计数器表示有没有比当前数更大的值(这个值有可能是0也有可能是1）没有与'1'匹配
        LL res = 0;
        for(int i = n; i; i--)
        {
            if(s[i] == '1')
            {
                if(cnt) res += i,cnt--;
                else cnt++;//比他大的数都匹配完了，那么这个'1'就要下一步与别人匹配1110这个序列前两个要相互匹配
            }
            else cnt ++;
        }
        cout << res << endl;
    }
    return 0;
}
```