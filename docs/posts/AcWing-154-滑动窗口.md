---
title: AcWing 154. 单调队列
date: 2023-03-12 20:03:29
tags:
- 滑动窗口
- 模版题
Categories:
- AcWing
- 模版题
- 滑动窗口（单调队列）
---

# [题意](https://www.acwing.com/problem/content/156/)

由于这是一个模版主要注意的是代码的实现，所以不再做题目展示

# CODE

```c++
#include<iostream>
#include<cstdio>

using namespace std;

const int N = 1e6+10;
int a[N], q[N];
int n, k;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);

    cin>>n>>k;

    for(int i = 0; i < n; i ++) cin>>a[i];

    int hh = 0, tt = -1;    //hh为队头，tt为队尾
    for(int i = 0; i < n; i ++)
    {
        // 判断单调队里面的队首是否在窗口里面，
        // 不在窗口里面的话就需要将对首往后面移动一位（因为队是单调的）
        if(hh <= tt && i - k + 1 > q[hh]) hh ++;    
        // 与队尾元素数值比较是否能构成单调队列（找最小值的时候就需要单调增序列，反之
        // 要使用while循环直到这个队列里面没有数比他还要小的时候
        while(hh <= tt && a[q[tt]] >= a[i]) tt --;  
        // 入队操作，单调队列放的是元素在原数组里面的下标
        q[++ tt] = i;   

        if(i - k + 1 >= 0) cout<<a[q[hh]]<<" ";
    }
    cout<<endl;

    //hh为队头，tt为队尾 这里一定注意要初始化队头和队尾指针！！！！
    hh = 0, tt = -1;    
    for(int i = 0; i < n; i ++)
    {
        // 判断单调队里面的队首是否在窗口里面，
        // 不在窗口里面的话就需要将对首往后面移动一位（因为队是单调的）
        if(hh <= tt && i - k + 1 > q[hh]) hh ++;   
        // 与队尾元素数值比较是否能构成单调队列
        while(hh <= tt && a[q[tt]] <= a[i]) tt --;  
        // 入队操作，单调队列放的是元素在原数组里面的下标
        q[++ tt] = i;   

        if(i - k + 1 >= 0) cout<<a[q[hh]]<<" ";
    }

}
```

