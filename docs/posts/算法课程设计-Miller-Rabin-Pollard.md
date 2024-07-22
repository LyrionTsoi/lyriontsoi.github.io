---
title: 算法课程设计- Miller-Rabin & Pollard
date: 2022-09-29 15:57:11
tags:
- 数论
- 课设
categories:
- 数论
- 素数
---

# 前言（引言）

## 课程设计目的：
现阶段的的RSA是作为世界上公认的最安全加密算法之一，许多的重要机构都在使用这RSA算法。一旦当今的RSA算法被成功破解世界上将会发生一系列重大的转折。在RSA算法的诞生之前每个几年或是十几年都会有一位密码学家破解出来，但是RSA算法诞生之后几十年来每个人都在挑战的这一个大数分解困难问题，并且无任何一人破解出来。 

## 课程设计范围：  
实现素数测试算法和分解因数算法，并可视化原理过程。  

## 工作描述及理论基础：  
通过费马小定理和二次探测原理实现素数测试。通过构造伪随机序列实现因数分解。  

## 预期结果和意义：  
实现大整数素数测试和因数分解。深刻体验在大数下对程序即使优化时间复杂度的O(√),都能带来很大的变化，也深刻体验到大数分解的世界级难题。   

## 可行性分析：    
大数分解问题人类至今仍未找到一个正确的认识，也就是我们还不能认识到素数内的深刻含义。当前我们实现的算法只是在认识素数道路上的坚实的一步，前路还远，需要不断探索数字的奥秘。    

## 总体设计：
大数通过数组进行模拟，所以基本的运算在本程序都需要的重载。并对算法进行更改。编写Miller-Rabin,Pollard’s Rho模块。高精度的快速幂和GCD函数作为辅助。

# 第一章 预处理及数据结构
## 1.1 头文件
```C++
#include <bits/stdc++.h> //工程性头文件
```
## 1.2 宏定义
算法编写习惯，将许多较长繁琐的简化
```C++
#define rep(i,a,n) for (int i=a;i<n;i++)
#define per(i,a,n) for (int i=n-1;i>=a;i--)
#define pb push_back
#define eb emplace_back
#define mp make_pair
#define all(x) (x).begin(),(x).end()
#define fi first
#define se secondb
#define SZ(x) ((int)(x).size())
```
## 1.3 结构体
有大数的存储结构
```C++
struct Data{
    int num[20100];	// 存储数据
    int len;	// 这个大数的位数
    char op;	// 这个大数正负性
    Data(){memset(num,0,sizeof(num)); len=1; op='+';}
};
```

# 第二章 函数
## 2.1 最大公约数 gcd(Data a, Data b)
功能设计：找出两个数之间的最大公约数
求两个正整数 a 和 b 的 最大公约数 d
则有 gcd(a,b) = gcd(b,a%b)
证明：
设a%b = a - k*b 其中k = a/b(向下取整)
    若d是(a,b)的公约数 则知 d|a 且 d|b 则易知 d|a-k*b 故d也是(b,a%b) 公约数
若d是(b,a%b)的公约数 则知 d|b 且 d|a-k*b 则 d|a-k*b+k*b = d|a 故而d|b 故而 d也是(a,b)的公约数
    因此(a,b)的公约数集合和(b,a%b)的公约数集合相同 所以他们的最大公约数也相同 证毕
代码如下：

```C++
	Data gcd(Data a,Data b) {   
	    if(b > turn(0)) return gcd(b,a%b);  
	    else return a;  
}  
```
## 2.2 高精度快速幂
功能设计：利用二进制优化计算一个大数的幂
计算 $a$ 的 $n$ 次方表示将 $n$ 个 $a$ 乘在一起：$a^{n} = \underbrace{a \times a \cdots \times a}_{n\text{ 个 a}}$。然而当 $a,n$ 太大的时侯，这种方法就不太适用了。不过我们知道：$a^{b+c} = a^b \cdot a^c,\,\,a^{2b} = a^b \cdot a^b = (a^b)^2$。二进制取幂的想法是，我们将取幂的任务按照指数的 **二进制表示** 来分割成更小的任务。  
如果把 $n$ 写作二进制为 $(n_tn_{t-1}\cdots n_1n_0)_2$，那么有：
$$
n = n_t2^t + n_{t-1}2^{t-1} + n_{t-2}2^{t-2} + \cdots + n_12^1 + n_02^0
$$

其中 $n_i\in\{0,1\}$。那么就有

$$
\begin{aligned}
a^n & = (a^{n_t 2^t + \cdots + n_0 2^0})\\\\
& = a^{n_0 2^0} \times a^{n_1 2^1}\times \cdots \times a^{n_t2^t}
\end{aligned}
$$

根据上式我们发现，原问题被我们转化成了形式相同的子问题的乘积，并且我们可以在常数时间内从 $2^i$ 项推出 $2^{i+1}$ 项。

这个算法的复杂度是 $\Theta(\log n)$ 的，我们计算了 $\Theta(\log n)$ 个 $2^k$ 次幂的数，然后花费 $\Theta(\log n)$ 的时间选择二进制为 1 对应的幂来相乘。
```C++
Data ksm(Data a, Data b, Data p)  // 快速幂
{
    if(b == turn(0)) return turn(1)%p;
    if(b == turn(1)) return a % p;
    Data t = ksm(a, b/turn(2), p);
    if(b % turn(2) == turn(0)) return t * t % p;
    else return (((t*t)%p)*a)%p;
}
```

## 2.3 素数测试 millerRabin(Data n)
功能设计：实现判断一个数是合数还是素数  

### 费马小定理
如果a是一个整数， p是一个素数一定满足

$$a^{p - 1} \equiv 1 \ mod \ p$$

证明费马小定理：
要知到$\left( \frac{p}{n} \right) = \frac{p!}{n!(p - n)!}modp = = 0$
首先看分子，分子上是有p这一项的，而分母$n!$ 和
$(p - n)!$很明显没有单独项`p`。再讨论能否通过多项相乘得到p呢？由于p是一个素数，其因子只有`1`和`p`自身，所以当没有`p`这一项是不可能相乘组合成`p`的。**所以通过上式我们知道了**$\left( \frac{p}{n} \right)$**是p的倍数**

$$(b + 1)^{p} \equiv (通过多项式展开) \rightarrow \left( \frac{p}{p} \right)b^{p} + \left( \frac{p}{p - 1} \right)b^{p - 1} + ... + \left( \frac{p}{0} \right)b^{0}\mspace{6mu} mod\ p$$

$$\Leftrightarrow 因为组合数和p取模等于0$$

$$\Leftrightarrow \left( \frac{p}{p} \right)b^{p} + \left( \frac{p}{0} \right)b^{0}\mspace{6mu} mod\ p$$

$$\Leftrightarrow (b + 1)^{p} \equiv b^{p} + 1\mspace{6mu} mod\ p$$

用$b = a - 1$ 代换到原公式当中就证得我们的费马小定理

费马小定理告诉我们只要p是质数就满足上面的等式。其逆定理经常用来证明这个数不是素数。也就是当p不是素数的情况下，就不会满足上式。这个也叫做**费马伪素数**。虽然费马逆定理可以基本证明绝大多数合数都是符合逆定理，但是有一类卡迈尔数是费马伪素数的反例。（最小的卡迈尔数是`561 = 3 * 11 * 17`）

### 二次探测定理

对于奇质数p和正整数e有

$$x^{2} \equiv 1\mspace{6mu} mod\ p^{e}$$

化简得到：\
$\Leftrightarrow (x - 1)(x + 1) \equiv 0\mspace{6mu} mod\ p^{e}$
也就是说，如果找到一个$a \neq \pm 1$,使得$a^{2} \equiv 1\mspace{6mu} mod\ n$,
那么说明n一定不是质数

证明：

$$x^{2^{i}k} - 1 = \left( x^{2^{i - 1}k} \right)^{2} - 1 = \left( x^{2^{i - 1}k} - 1 \right)\left( x^{2^{i - 1}k} + 1 \right)$$

$$\Leftrightarrow \left( x^{k} - 1 \right)\left( x^{k} + 1 \right)\left( x^{2k} + 1 \right)\left( x^{4k} + 1 \right)...\left( x^{2^{i - 1}k} + 1 \right)$$

当p是素数的时候 $a \in \lbrack 1,p\rbrack$,有费马小定理

$$a^{p - 1} \equiv 1\mspace{6mu} mod\ p$$

当$p - 1$等于$2^{i}k$时候可以由上式知道

$$\left( x^{k} - 1 \right)\left( x^{k} + 1 \right)\left( x^{2k} + 1 \right)\left( x^{4k} + 1 \right)...\left( x^{2^{i - 1}k} + 1 \right) \equiv 0\mspace{6mu} mod\ p$$

根据上式知道如果p是素数，一定有$x^{k} \equiv 1\mspace{6mu} mod\ p$
**OR** $x^{2^{i}k} \equiv - 1\mspace{6mu} mod\ p$
运用其逆定理我们就可以知道算法来**伪素数**

### Miller-Rabin 素数测试 CODE
```C++
bool millerRabin(Data n)
{
    // 偶数一定点不是素数（除2外）
    // < 3的数只有2是素数
    Data num3 = turn(3), num2 = turn(2), num0 = turn(0), num1 = turn(1);
    if(n < num3 || n % num2 == num0) return n == num2;
    // 费马小定理 a^(p-1) mod p同余1
    // 使用费马小定理转化成二次探测
    // 即用(2^e)k 表示成 n-1
    // b是次数
    Data a = n - num1;
    int b = 0;
    while(a % num2 == num0) a = a / num2, b = b + 1;
    // 表头(需要可视化)
    puts("millerRabin算法");
    puts("底数x\t次数a\t与p同余\tn");

    // test_time 为测试次数,建议设为不小于 8
    // 的整数以保证正确率,但也不宜过大,否则会影响效率
    for(int i = 1, j; i <= test_time; i ++)
    {
        // 理论上1<=a<p, 但是底数为1来计算没什么大的意义只会浪费测试次数(其实底数也没有特定的要求，随机数即可)
        Data x = turn(1ll * rand());
        // 快速幂
        // Data v = quickPow(x,a,n);
        Data v = ksm(x,a,n);
        print(x),printf("\t"),print(a), printf("\t"),print(v),printf("\t"),print(n),puts("");
        // printf("%lld\t%lld\t%lld\t%lld\n", x, a, v, n);
        if(v == num1) continue;

        for(j = 0; j < b; j ++)
        {
            if(v == n - num1) break;
            a = a * num2;
            v = v * v % n;
            print(x),printf("\t"),print(a), printf("\t"),print(v),printf("\t"),print(n),puts("");
            // printf("%lld\t%lld\t%lld\t%lld\n", x, a, v, n);
        }
        // 如果测完了之后都没有同余就说明当前的数不是素数
        if(j >= b) return 0;
    }
    return 1;
}
```

## 2.4 Pollard 大数分解 pollard_rho(Data n)
功能设计： 实现寻找大数的因子  

### 生日问题
#### 问题描述
生日问题是指最少需要几人，当中的两个人生日相同的概率才会大于50%。这个题目的答案是23人。
这就意味着在一个典型的标准小学班级（30人）中，存在两人生日相同的可能性更高。对于60或者更多的人，这种概率会大于99%。这个问题有时也被称做生日悖论，但从引起逻辑矛盾的角度来说生日悖论并不是一种悖论，它被称作悖论只是因为这个数学事实与一般直觉相抵触而已。大多数人会认为，23人中有2人生日相同的概率应该远远小于50%。计算与此相关的概率被称为生日问题，在这个问题之后的数学理论已被用于设计著名的密码攻击方法：生日攻击。

#### 问题解释
可以把生日悖论理解成一个盲射打靶的问题。对于一个23人的房间，先考虑问题的补集：23人生日两两不同的概率是多少？为此，可以让23个人依次进入，那么每个人生日都与其他人不同的概率依次是1，364/365，363/365，362/365，361/365，等等。先进入房间的这些人生日两两不同的概率是很大的，比如说前面5个是1×364/365×363/365×362/365×361/365=97.3%。而对于最后进入房间的几个人情况就完全不同。最后几个人进入房间并且找不到同生日者的概率是...
345/365，344/365，343/365。可以把这种概率看成对一张靶的盲射：靶上有365个小格，其中有17个左右是黑格，其余是白格。假设每枪必中靶并且分布符合几何概型的话，那么连续射12枪左右任何一发都没有击中黑格的概率（投射于房间里的人生日都两两不同）是多少呢？想必大家立即会感觉到这个概率十分微小。

理解生日悖论的关键，在于考虑上述"依次进入房间"模型中最后几个进入房间的人"全部都没碰到相同生日的人"概率有多大这件事情。

简而言之，大多数人之所以会认为23人中有2人生日相同的概率应该远远小于50%，是因为将问题理解为"其他22人与你的生日相同的概率"，而非问题本意"23人之中两两之间存在生日相同"。如果考虑到这一点，直觉上会将原来的概率乘以23（注意：此算法并不正确），则会意识到概率很大了。

#### CODE
```C++
    #include <bits/stdc++.h>
    using namespace std;
    // head

    int main()
    {
        double mult = 1.0;
        double ans = 1.0;

        // 23 个人
        for(int i = 1; i <= 23; i ++)
        {
            mult *= (365 - i + 1) / 365.0;
        }

        ans -= mult;

        cout << ans << endl;

        return 0;
    }
```
我们可以根据上面的代码计算出来只要一个班有23个人的话，两个人在同一个天生日的概率就大概在`0.507297`

### 构造伪随机函数

我们通过 $f(x) = \left( x^{2} + c \right)\ mod\ n$ 来生成一个随机数序列
$\{ x_{i}\}$，其中 $c = rand()$，是一个随机的常数。

随机取一个
$x_{1},令x_{2} = f\left( x_{1} \right),x_{3} = f\left( x_{2} \right),\ldots,x_{i} = f\left( x_{i - 1} \right)$，在一定范围内可以认为这个数列是"随机"的。

举个例子，设 $N = 50,c = 2,x_{1} = 1$ $f(x)$ 生成的数据为

$$1,3,11,23,31,11,23,31,\ldots$$

可以发现数据在 3 以后都在 11,23,31 之间循环，这也是 $f(x)$
被称为伪随机函数的原因。

如果将这些数如下图一样排列起来，会发现这个图像酷似一个
$\rho$，算法也因此得名 rho。

### 实现

-   我们知道对于每一个数来说一定存在一个最大公约数gcd
-   根据生日悖论知道，
    即使对于一小部分人来说，两个人同一个天生日的概率依然会是很高
-   floyd判环

算法： **通过floyd优化后的算法只能分解出来任意一个因数** 1. 从随机 x 和
c 开始。取 y 等于 x 和 f(x) = x 2 + c。 2. 虽然没有获得除数 2.1 将 x
更新为 f(x)（模 n）\[乌龟移动\] 2.2 将 y 更新为 f(f(y)) (模 n) \[Hare
Move\] 2.3 计算 \|xy\| 的 GCD 和 n 如果 GCD 不是统一的 如果 GCD 为
n，则使用另一组 x、y 和 c 从步骤 2 开始重复 否则 GCD 是我们的答案

### Pollard 算法
```C++
// 找出大数中的其中一个质因子
Data pollard_rho(Data n)
{
    // cout << "adasd" << endl;
    // 伪随机序列 f(x) = x^2 + c mod n
    Data num3 = turn(3), num2 = turn(2), num0 = turn(0), num1 = turn(1);
    Data c = turn(1ll * rand()) % (n-num1) + num1;
    Data t = f(turn(1ll * rand()) % (n-num1) + num1, c, n);    Data r = f( f( turn(1ll * rand()) % (n-num1) + num1, c, n ), c, n );
    // cout << c.len << ' ' << t.len << ' ' << r.len << endl;
    // cout << c.op << ' ' << t.op << ' ' << r.op << endl;
    // print(c), printf("\t"),print(t),printf("\t"),print(r),puts("");
    Data d = num1;
    // 正因为mod n 算法我们知道最后会呈一个周期性的状态，所以当r追赶上的t的时候还没有退出循环，那么就意味找不到的我们想要的结果
    // cout << t << ' ' << r << endl;

    // 这里的输出需要可视化
    puts("pollard_rho算法");
    printf("c = "),print(c),puts("");
    // printf("c = %ld\n", c);
    puts("xi + 1 = f(x)\txi+1 = f(y)\tc\td = gcd(|x-y|,n)");
    while(t != r)
    {
        cout << "asdsahdkaj" << endl;
        // 通过生日悖论知道两个数有相同的最大公约数的概率其实是很大的
        Data tmp;
        if( (t - r) > num0) tmp = t - r;
        else tmp = r - t;
        cout << 1 << endl;
        d = gcd(tmp, n); 
        print(t),printf("\t"),print(r), printf("\t"),print(c),printf("\t"),print(d),puts("");
        // printf("%lld\t%lld\t%lld\t%lld\n", t, r, c, d);
        // cout << d << endl;
        // gcd不唯一且非平凡因子时候才能返回值
        if(d > num1) return d;
        // gcd == n OR 1 重复寻找随机序列对
        t = f(t,c,n);
        r = f(f(r,c,n),c,n);
    }
    return n;
}
```

# 第三章 主函数
## 3.1 功能菜单  
功能设计： 让用户选择三个功能  
1. 你认为它是一个合数，调用Pollard算法对它进行因数分解
2. 你认为它是一个素数，调用Miller-Rabbin算法对它进行测试确认
3. 你不知道它是合数还是素数，先尝试Pollard算法对它进行因数分解，打印分解的结果.如果分解失败，转而调用Miller-Rabbin对它进行检查，报告是否素数

# 课程设计代码
## 可视化CODE
```C++
#define _CRT_SECURE_NO_WARNINGS
    #include<vector>
    #include<time.h>
    #include<iostream>
    #include<conio.h>
    #include<graphics.h>

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
    typedef pair<int, int> PII;
    typedef double db;
    const ll mod = 1000000007;
    const int INF = 0x3f3f3f3f;
    ll gcd(ll a, ll b) { return b ? gcd(b, a%b) : a; }
    // head
    const int test_time = 8;
    void INPUTCENT(char *pst, int x1, int y1, int x2, int y2);
    void INPUTCENT(ll nums, int x1, int y1, int x2, int y2);
    void DrawMBT();
    void DrawPBT();
    void INITFACE();
    // 二进制优化快速幂
    ll quickPow(ll a, ll b, ll p)
    {
        ll res = 1 % p;
        while (b)
        {
            if (b & 1) res = res * a % p;
            a = a * a % p;
            b >>= 1;
        }
        return res;
    }

    // 素数测试
    bool millerRabin(ll n)
    {
        // 偶数一定点不是素数（除2外）
        // < 3的数只有2是素数
        if (n < 3 || n % 2 == 0) return n == 2;
        // 费马小定理 a^(p-1) mod p同余1
        // 使用费马小定理转化成二次探测
        // 即用(2^e)k 表示成 n-1
        // b是次数
        ll a = n - 1, b = 0;
        while (a % 2 == 0) a /= 2, ++b;
        // 表头(需要可视化)
        puts("millerRabin算法");
        puts("底数x\t次数a\t与p同余\tn");

        // test_time 为测试次数,建议设为不小于 8
        // 的整数以保证正确率,但也不宜过大,否则会影响效率
        INITFACE();
        DrawMBT();
        // 第一行的数据
        int H = 1;
        for (int i = 1, j; i <= test_time; i++)
        {
            // 理论上1<=a<p, 但是底数为1来计算没什么大的意义只会浪费测试次数
            ll x = 1ll * rand() % (n - 1) + 1;
            // 快速幂
            ll v = quickPow(x, a, n);
            printf("%lld\t%lld\t%lld\t%lld\n", x, a, v, n);


            // 第L列的数据
            int L = 0;
            INPUTCENT(x, 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
            L++;
            INPUTCENT(a, 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
            L++;
            INPUTCENT(v, 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
            L++;
            INPUTCENT(n, 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
            L++;
            H++;
            if (v == 1) continue;

            for (j = 0; j < b; j++)
            {
                if (v == n - 1) break;
                v = v * v % n;
                printf("%lld\t%lld\t%lld\t%lld\n", x, a, v, n);
                L = 0;
                INPUTCENT(x, 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
                L++;
                INPUTCENT(a, 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
                L++;
                INPUTCENT(v, 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
                L++;
                INPUTCENT(n, 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
                L++;
                H++;
            }
            // 如果测完了之后都没有同余就说明当前的数不是素数
            if (j >= b) return 0;
        }
        /*_getch();*/
        return 1;
    }

    // 构造伪随机序列
    ll f(ll x, ll c, ll n)
    {
        return (x * x + c) % n;
    }

    // 找出大数中的其中一个质因子
    ll pollard_rho(ll n)
    {
        // 伪随机序列 f(x) = x^2 + c mod n
        ll c = 1ll * rand() % (n - 1) + 1;
        ll t = f(1ll * rand() % (n - 1) + 1, c, n);
        ll r = f((1ll * rand() % (n - 1) + 1, c, n), c, n);
        ll d = 1;
        // 正因为mod n 算法我们知道最后会呈一个周期性的状态，所以当r追赶上的t的时候还没有退出循环，那么就意味找不到的我们想要的结果
        // cout << t << ' ' << r << endl;

        // 这里的输出需要可视化
        puts("pollard_rho算法");
        printf("c = %ld\n", c);
        puts("xi + 1 = f(x)\txi+1 = f(y)\tc\td = gcd(|x-y|,n)");
        INITFACE();
        DrawPBT();
        // 第一行的数据
        int H = 1;
        while (t != r)
        {
            // 通过生日悖论知道两个数有相同的最大公约数的概率其实是很大的
            d = gcd(abs(t - r), n);
            printf("%lld\t%lld\t%lld\t%lld\n", t, r, c, d);
            int L = 0;
            INPUTCENT(t, 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
            L++;
            INPUTCENT(r, 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
            L++;
            INPUTCENT(c, 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
            L++;
            INPUTCENT(d, 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
            L++;
            H++;
            // cout << d << endl;
            // gcd不唯一且非平凡因子时候才能返回值
            if (d > 1 && d != n) return d;
            // gcd == n OR 1 重复寻找随机序列对
            t = f(t, c, n);
            r = f(f(r, c, n), c, n);
        }
        //_getch();
        return n;
    }



    int main()
    {
        initgraph(800, 800, SHOWCONSOLE);
        setbkcolor(0x3a0a0a);                                                   // 设置背景颜色为黑色
        cleardevice();

        puts("-----------------------------------------------------------------------------------------------------------------------------------------");
        puts("\t\t\t功能菜单");
        puts("1. 你认为它是一个合数，调用Pollard算法对它进行因数分解");
        puts("2. 你认为它是一个素数，调用Miller-Rabbin算法对它进行测试确认");
        puts("3. 你不知道它是合数还是素数，先尝试Pollard算法对它进行因数分解，打印分解的结果.如果分解失败，转而调用Miller-Rabbin对它进行检查，报告是否素数。");

        int p;
        ll test;
        puts("您需要先选择一个功能，然后在输入您需要测试的数据（以空格或换行分割）");
        cin >> p >> test;

        // 随机种子
        srand(time(NULL));
        switch (p)
        {
        case 1:
        {
                  if (millerRabin(test))
                  {
                      puts("这是一个素数");
                      break;
                  }
                  ll d = pollard_rho(test);
                  while (d == test) d = pollard_rho(test);
                  printf("合数:%lld其中的一个因子为%lld\n", test, d);

        }  break;
        case 2:
        {
                  if (millerRabin(test)) puts("这是一个素数");
                  else puts("这不是一个素数");

        }  break;
        case 3:
        {
                  if (!millerRabin(test))
                  {
                      MessageBox(NULL,"开始绘制下一个","提示",IDYES);
                      ll d = pollard_rho(test);
                      printf("合数:%lld其中的一个因子为%lld\n", test, d);
                  }
                  else puts("这是一个素数，不存在非平凡因子");

        } break;
        }
        _getch();
        return 0;
    }

    void INPUTCENT(char *pst, int x1, int y1, int x2, int y2)
    {
        setlinestyle(PS_SOLID | PS_ENDCAP_SQUARE, 3);
        setlinecolor(0xFF5555);
        setfillcolor(0x3a0a0a);
        fillrectangle(x1, y1, x2, y2);
        setbkmode(TRANSPARENT);
        settextcolor(0xf4b1a4);
        settextstyle(20, 0, _T("楷体"));
        RECT r = { x1, y1, x2, y2 };
        drawtext(pst, &r, DT_CENTER | DT_VCENTER | DT_SINGLELINE);
    }

    void INPUTCENT(ll nums, int x1, int y1, int x2, int y2)
    {
        setbkmode(TRANSPARENT);
        settextcolor(0xEFFFFE);
        settextstyle(15, 0, _T("consolas"));
        char s[128];
        sprintf(s, "%lld", nums);
        RECT r = { x1, y1, x2, y2 };
        drawtext(s, &r, DT_CENTER | DT_VCENTER | DT_SINGLELINE);
    }

    void DrawMBT()
    {
        // 第一行的数据
        int H = 0;
        // 第L列的数据
        int L = 0;
        INPUTCENT("x底数", 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
        L++;
        INPUTCENT("a次数", 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
        L++;
        INPUTCENT("与x同余", 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
        L++;
        INPUTCENT("n", 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
        L++;
    }

    void DrawPBT()
    {
        // 第一行的数据
        int H = 0;
        // 第L列的数据
        int L = 0;
        INPUTCENT("Xi+1=f(x1)", 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
        L++;
        INPUTCENT("Xi+1=f(f(y1))", 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
        L++;
        INPUTCENT("c", 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
        L++;
        INPUTCENT("d=GCD(|x-y|,n)", 40 + 190 * L, 40 + H * 20, 40 + 190 * (L + 1), 60 + H * 20);
        L++;
    }
    void INITFACE()
    {
        cleardevice();
        setlinecolor(0XFD0392);                                                 // 蓝色
        setlinestyle(PS_SOLID | PS_ENDCAP_FLAT, 2);                             // 最细的线宽度为2，其次为4
        roundrect(2, 2, 798, 798, 12, 12);                                      // 圆角矩形
        setlinestyle(PS_SOLID | PS_ENDCAP_SQUARE, 0);
        setlinecolor(0x5555FF);
        for (int i = 0; i < 39; i++)
        {
            line(10, 40 + i * 20, 790, 40 + i * 20);
        }
        for (int i = 0; i < 4; i++)
        {
            line(40 + i * 190, 10, 40 + i * 190, 782);
        };
    }
```

## 3.2 根据用户的选择功能对其相应的输出

# 结论
 在开发素数测试和大数分解的过程首先从容易的程序开始编写，从long long int到后来使用的黑科技__int128,再经过一遍遍的sf错误、答案错误的更正，现在才形成了大整数（现在拓展位了两万位）的程序。虽然对于大数（位数超过100位的情况）运行程序并没有那么的快，有因为不确定算法的原因可能造成的错误答案，但是还是形成了现在的代码。在过程中因为高进度算法的原因不能使用二进制移位操作而造成程序的SF错误，有因为大数不能通过普通的printf输出，还有多个环境下测试出来的结果不一样的问题。还有在实现本程序最后困扰我的问题，在本程序的开发过程中不断遇到了SF错误，大部分的SF错误都是应为某个数组，某个指针越界而造成的，但是本程序最后一个SF错误经过多天的思考，最后认定为栈的大小问题而造成的，通过`ulimit -s 102400`命令在linux系统下，成功的跑出。

# 谢辞
 我要感谢我的舍友，是他们在我改不出bug的时候鼓舞了我，是他们给我的精神上的支持才本程序才能成为现在这个版本。我还要感谢我的同学们，在我的疫情隔离在家的时候，是他们把课设的要求准确的传达给我。我还要感谢所有我咨询过问题的朋友们，是他们的一一解答所有的问题才能引刃而解。同时我要感谢我的老师，是老师的指导下，才能得以完成。
