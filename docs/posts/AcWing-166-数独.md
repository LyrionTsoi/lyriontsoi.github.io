---
title: AcWing 166. 数独
date: 2022-11-10 16:59:53
tags:
- dfs
- 剪枝
actegories:
- AcWing
- 搜索
- dfs剪枝
---

# [AcWing 166. 数独](https://www.acwing.com/problem/content/168/)
数独 是一种传统益智游戏，你需要把一个 9×9 的数独补充完整，使得数独中每行、每列、每个 3×3 的九宫格内数字 1∼9 均恰好出现一次。

请编写一个程序填写数独。

输入格式
输入包含多组测试用例。

每个测试用例占一行，包含 81 个字符，代表数独的 81 个格内数据（顺序总体由上到下，同行由左到右）。

每个字符都是一个数字（1−9）或一个 .（表示尚未填充）。

您可以假设输入中的每个谜题都只有一个解决方案。

文件结尾处为包含单词 end 的单行，表示输入结束。

输出格式
每个测试用例，输出一行数据，代表填充完全后的数独。

## 题解
数独的规则
1. 每一行都包含了1～9
2. 每一列都包含了1～9
3. 每一个3x3都包含了1~9

## CODE
```C++
#include <bits/stdc++.h>
using namespace std;
#define rep(i,a,n) for (int i=a;i<n;i++)
#define per(i,a,n) for (int i=n-1;i>=a;i--)
#define pb push_back
#define eb emplace_back
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
const int N = 9;

// 这里的三个数组记录的是当前列/行/3x3内的所有合法方案
int col[N], row[N], cell[3][3];
char str[100];
int ones[1 << N], mp[1 << N];  // ones数组记录的是某一个状态内有多少个1， mp记录的是1所在的位置

// init是对col, row, cell数组初始化操作
void init()
{
    // 这里的初始化把col & row 初始化为全1
    for(int i = 0; i < N; i ++) col[i] = row[i] = (1 << N) - 1;

    for(int i = 0; i < 3; i ++)
        for(int j = 0; j < 3; j ++)
            cell[i][j] = (1 << N) - 1;
}

// is_set == true的时候表示修改现场
// is_set == fasle的时候表示恢复现场
void draw(int x, int y, int t, bool is_set)
{
    if(is_set) str[x * 9 + y] = '1' + t;
    else str[x * 9 + y] = '.';

    int v = 1 << t;
    if(!is_set) v = -v;

    row[x] -= v;
    col[y] -= v;
    cell[x/3][y/3] -= v;
}

// 获取最低有效位
inline int lowbit(int x)
{
    return x & -x;
}

// 获取当前位置上的所有合法状态
int get(int x, int y)
{
    // 这里类似使用了一个状压的技巧，把状态压缩成二进制
    return row[x] & col[y] & cell[x/3][y/3];
}

bool dfs(int cnt)
{
    if(!cnt) return true; // 可行性剪枝

    int minv = 10; // 寻找最少的状态这一个点进行更新，原因是因为状态越少，分支数目越少
    int x ,y;   // location
    for(int i = 0; i < N; i ++)
        for(int j = 0; j < N; j ++)
            if(str[i * 9 + j] == '.')
            {
                int state = get(i,j);
                if(ones[state] < minv)
                {
                    minv = ones[state];
                    x = i, y = j;
                }
            }

    int state = get(x,y);
    for(int i = state; i; i -= lowbit(i))
    {
        int t= mp[lowbit(i)]; // 找出来最低位1对应的坐标
        draw(x,y,t,true);
        if(dfs(cnt-1)) return true;
        draw(x,y,t,false);
    }

    return false;
}

int main()
{
    // 打表初始化ones数组和mp数组
    for(int i = 0; i < N; i ++) mp[1 << i] = i;
    for(int i = 0; i < 1 << N; i ++)
        for(int j = 0; j < N; j ++)
            ones[i] += i >> j & 1;

    while(cin >> str, str[0] != 'e')
    {
        init();

        int cnt = 0; // 记录有多少个要填的数
        for(int i = 0, k = 0; i < N; i ++)
            for(int j = 0; j < N; j ++, k ++)
                if(str[k] != '.')
                {
                    int t = str[k] - '1';
                    draw(i,j,t,true);
                }
                else cnt ++;

        dfs(cnt);
        puts(str);
    }


    return 0;
}
```