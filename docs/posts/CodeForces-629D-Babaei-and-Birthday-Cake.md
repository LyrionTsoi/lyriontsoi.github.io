---
title: CodeForces - 629D Babaei and Birthday Cake
date: 2022-07-30 13:47:05
tags:
- 线段树优化dp问题
- 动态规划
- 线段树
categories:
- CodeForces
- 动态规划
- 线段树优化
---

## [CodeForces - 629D Babaei and Birthday Cake 线段树优化dp](https://vjudge.net/problem/CodeForces-629D)

As you know, every birthday party has a cake! This time, Babaei is going to prepare the very special birthday party's cake.
Simple cake is a cylinder of some radius and height. The volume of the simple cake is equal to the volume of corresponding cylinder. Babaei has n simple cakes and he is going to make a special cake placing some cylinders on each other.
However, there are some additional culinary restrictions. The cakes are numbered in such a way that the cake number i can be placed only on the table or on some cake number j where j < i. Moreover, in order to impress friends Babaei will put the cake i on top of the cake j only if the volume of the cake i is strictly greater than the volume of the cake j.
Babaei wants to prepare a birthday cake that has a maximum possible total volume. Help him find this value.

Input
The first line of the input contains a single integer n (1 ≤ n ≤ 100 000) — the number of simple cakes Babaei has.
Each of the following n lines contains two integers ri and hi (1 ≤ ri, hi ≤ 10 000), giving the radius and height of the i-th cake.

Output
Print the maximum volume of the cake that Babaei can make. Your answer will be considered correct if its absolute or relative error does not exceed $10^-6$.

Sample 1
Input
```
2
100 30
40 10
```
Output
```
942477.796077000
```
Sample 2
Input
```
4
1 1
9 7
1 4
10 7
```
Output
```
3983.539484752
```

### 题解
题目要求在全部合法方案的中选择cake的体积最大
合法方案：当我们选择编号为j时候
1. 编号j一定要大于i的编号
2. j的体积一定要严格小于i

普通的dp
dp[i]： 表示以i结尾的最大体积
所以这里就不能以从i-1转移过来一定是要遍历完全部的1～i-1才可以找到最优解
所以普通的dp是$O(n^2)$
n是1e5所以我们需要对dp做优化

线段树的优化dp
依然是dp[i]，但是我们在作1～i-1区间查询如果使用线段树那么就可以转化成$O(nlogn)$
