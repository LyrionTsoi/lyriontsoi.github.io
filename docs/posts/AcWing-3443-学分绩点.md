---
title: AcWing 3443. 学分绩点
date: 2022-12-30 20:37:24
tags:
- 模拟
- 考研机试题
Categories:
- AcWing
- 考研机试题
---

# 题目描述

北京大学对本科生的成绩施行平均学分绩点制（GPA）。

既将学生的实际考分根据不同的学科的不同学分按一定的公式进行计算。

公式如下：

```
实际成绩     绩点 
90——100     4.0 
85——89      3.7 
82——84      3.3 
78——81      3.0 
75——77      2.7 
72——74      2.3 
68——71      2.0 
64——67      1.5 
60——63      1.0 
60以下        0
```

一门课程的学分绩点 == 该课绩点 × 该课学分

总评绩点 == 所有学科学分绩点之和 / 所有课程学分之和

现要求你编写程序求出某人 A 的总评绩点（GPA）。

## 输入格式

第一行，总的课程数 n；

第二行，相应课程的学分（两个学分间用空格隔开）；

第三行，对应课程的实际得分；

此处输入的所有数字均为整数。

## 输出格式

输出有一行，总评绩点，精确到小数点后 2 位小数。

## 数据范围

1≤n≤10,
学分取值范围 [1,10][1,10],
课程得分范围 [0,100][0,100]。

## 输入样例：

```
5
4 3 4 2 3
91 88 72 69 56
```

## 输出样例：

```
2.52
```

## CODE

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
const int N = 15;

int n;
double res;
// arr[i][0]:表示的第i门学科的学分，arr[i][1]：表示第i门学科的考试分数
int arr[N][2]; 


int main()
{
    int gpa = 0;
    // 读入课程数
    cin >> n;
    // 读入课程学分
    for(int i = 0; i < n; i ++)
    {
        cin >> arr[i][0];
        gpa += arr[i][0];
    }
    
    // 读入课程成绩
    for(int i = 0; i < n; i ++)
        cin >> arr[i][1];

    double sc = 0;
    // 计算总绩点之和
    for(int i = 0; i < n; i ++)
    {
        if(arr[i][1] >= 90)
            sc += 4.0 * arr[i][0];
        else if(arr[i][1] >=85)
            sc += 3.7 * arr[i][0];
        else if(arr[i][1] >= 82)
            sc += 3.3 * arr[i][0];
        else if(arr[i][1] >= 78)
            sc += 3.0 * arr[i][0];
        else if(arr[i][1] >= 75)
            sc += 2.7 * arr[i][0];
        else if(arr[i][1] >= 72)
            sc += 2.3 * arr[i][0];
        else if(arr[i][1] >= 68)
            sc += 2.0 * arr[i][0];
        else if(arr[i][1] >= 64)
            sc += 1.5 * arr[i][0];
        else if(arr[i][1] >= 60)
            sc += 1.0 * arr[i][0];
    }

    res = sc / gpa;
    printf("%0.2lf",res);

    return 0;
}
```

**y 之优雅**

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
const int N = 15;

int n;
double res;
// arr[i][0]:表示的第i门学科的学分，arr[i][1]：表示第i门学科的考试分数
int arr[N][2]; 
int key[10] = {0, 60, 64, 68, 72, 75, 78, 82, 85, 90};
double value[10] = {0, 1.0, 1.5, 2.0, 2.3, 2.7, 3.0, 3.3, 3.7, 4.0};

double get(int x)
{
    for(int i = 9; i; i --)
        if(x >= key[i])
            return value[i];
    return 0;
}

int main()
{
    int gpa = 0;
    // 读入课程数
    cin >> n;
    // 读入课程学分
    for(int i = 0; i < n; i ++)
    {
        cin >> arr[i][0];
        gpa += arr[i][0];
    }
    
    // 读入课程成绩
    for(int i = 0; i < n; i ++)
        cin >> arr[i][1];

    double sc = 0;
    // 计算总绩点之和
    for(int i = 0; i < n; i ++)
        sc += get(arr[i][1]) * arr[i][0];

    res = sc / gpa;
    printf("%0.2lf",res);

    return 0;
}
```

