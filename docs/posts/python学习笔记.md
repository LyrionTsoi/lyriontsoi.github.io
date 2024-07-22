---
title: python学习笔记
date: 2022-09-09 16:17:27
tags:
- python
- 学习笔记
categories:
- python
- 学习笔记
---

## 1.python解释器
在一开始的时候没有必要直接去使用ide, 在mac上`/usr/bin `使用命令python3即可以调出pyton解释器

## 2. 数据结构
### 2.1 列表详解
Python 支持多种 复合 数据类型，可将不同值组合在一起。最常用的*列表*，是用方括号标注，逗号分隔的一组值。*列表*可以包含不同类型的元素，但一般情况下，各个元素的类型相同
```python
squares = [1, 4, 9, 16, 25]
squares
```
和字符串一样也支持索引
```python
squares[0]  # indexing returns the item
squares[-1]
squares[-3:]  # slicing returns a new list
```
#### 2.1.1 函数
append(x) 与 insert(i,x)
 insert函数必须告诉插入到哪个位置，如果默认插在最后的话那么就退化成append()，设计逻辑上是不对的

remove(x) 与 pop([i])
 remove函数是根据函数值进行索引去除
 pop函数是根据下标（索引值）来去除数值

index(x[,start[,end]])
用法一 从头开始搜索我们指定的值
 ```python
 list1 = ['Google', 'Runoob', 'Taobao']
 print ('Runoob 索引值为', list1.index('Runoob'))
 print ('Taobao 索引值为', list1.index('Taobao'))
 ```
 输出
 ```
 Runoob 索引值为 1
 Taobao 索引值为 2

 ```
 用法二 指定位置开始搜索
 ```python
 list1 = ['Google', 'Runoob', 'Taobao']
 print ('Runoob 索引值为', list1.index('Runoob',1)) # 从下标为1的位置开始检索
 #  异常报错
 print ('Runoob 索引值为', list1.index('Runoob',2))
 ```
 输出
 ```
 Runoob 索引值为 1
 触发 ValueError 异常。
 ```

sort(*,key = None, reverse = False)
 key 表示按照什么规则 比如可以按照长度规则或是按照数值大小规则来进行排序
 reverse：True 表示要降序，某人为False

### 2.2 堆栈
使用列表方法实现堆栈非常容易，最后插入的最先取出（“后进先出”）。把元素添加到堆栈的顶端，使用 append() 。从堆栈顶部取出元素，使用 pop() ，不用指定索引。例如：

```Python
stack = [3, 4, 5]
stack.append(6)
stack.append(7)
stack

stack.pop()

stack

stack.pop()

stack.pop()

stack
```

### 2.3 用列表实现队列
列表也可以用作队列，最先加入的元素，最先取出（“先进先出”）；然而，列表作为队列的效率很低。因为，在列表末尾添加和删除元素非常快，但在列表开头插入或移除元素却很慢（因为所有其他元素都必须移动一位）。

实现队列最好用 collections.deque，可以快速从两端添加或删除元素。例如：

**要注意的是使用deque要引入头文件使用语句  `from collection import deque`**
```python
from collections import deque   # 如何直接引入collections库的话下文的queue = collections.deque()
queue = deque(["Eric", "John", "Michael"])
queue.append("Terry")           # Terry arrives
queue.append("Graham")          # Graham arrives
queue.popleft()                 # The first to arrive now leaves

queue.popleft()                 # The second to arrive now leaves

queue                           # Remaining queue in order of arrival
```

**但是我们可以从引入的这个库`deque`的名字我们可以知道这是一个双端队列，不仅仅是可以使用队列的*队头出队队尾入队*操作，还可以在两端都可以走进队出队操作，这里经常会在双端队列bfs算法中会使用**

#### 2.3.1 函数
 * queue.append() 表示队尾入队操作
 * queue.appendleft() 表示队头入队操作
 * queue.popleft() 表示队头元素弹出
 * queue.pop() 表示队尾元素弹出
 
## 3. 其他流程控制工具
### 3.1 if 语句
```python
x = int(input("Please enter an integer: "))

if x < 0:
    x = 0
    print('Negative changed to zero')
elif x == 0:
    print('Zero')
elif x == 1:
    print('Single')
else:
    print('More')
```

### 3.2 for语句
**python的for语句并不需要迭代值和c++中的auto迭代数组非常像**
```C++
int arr[10];
for(auto i : arr)
    cout << i << endl;
```
```python
# Measure some strings:
words = ['cat', 'window', 'defenestrate']
for w in words:
    print(w, len(w))
```

**遍历集合时修改集合的内容，会很容易生成错误的结果。因此不能直接进行循环，而是应遍历该集合的副本或创建新的集合：**
创建副本->修改副本->将修改的副本复制会原文件当中
```python
# Create a sample collection
users = {'Hans': 'active', 'Éléonore': 'inactive', '景太郎': 'active'}

# Strategy:  Iterate over a copy
for user, status in users.copy().items():
    if status == 'inactive':
        del users[user]

# Strategy:  Create a new collection
active_users = {}
for user, status in users.items():
    if status == 'active':
        active_users[user] = status
```

### 3.3 range()函数
**可以使用range函数来实现迭代的次数**
内置函数 range() 常用于遍历数字序列，该函数可以生成算术级数：
```python
for i in range(5):
    print(i)
```
还有一些其他的奇奇怪怪的用法
```python
>>> list(range(5, 10))
[5, 6, 7, 8, 9]

>>> list(range(0, 10, 3))
[0, 3, 6, 9]

>>> list(range(-10, -100, -30))
[-10, -40, -70]
```
range() 和 len() 组合在一起，可以按索引迭代序列：
```python
>>> a = ['Mary', 'had', 'a', 'little', 'lamb']
>>> for i in range(len(a)):
...     print(i, a[i])
...
0 Mary
1 had
2 a
3 little
4 lamb
```
range() 和 len() 组合在一起，可以按索引迭代序列：
```python
>> range(10)
range(0, 10)
```
range() 和 sum()组成在一起
```
>>> sum(range(4))  # 0 + 1 + 2 + 3
6
```

### 3.4. 循环中的 break、continue 语句及 else 子句
`break`,`continue`语句基本与c/c++中的语法无差。重要讲一下区别
```python
>>> for n in range(2, 10):
...     for x in range(2, n):
...         if n % x == 0:
...             print(n, 'equals', x, '*', n//x)
...             break
...     else:
...         # loop fell through without finding a factor
...         print(n, 'is a prime number')
...
2 is a prime number
3 is a prime number
4 equals 2 * 2
5 is a prime number
6 equals 2 * 3
7 is a prime number
8 equals 2 * 4
9 equals 3 * 3
```
**看上面这个语句else子句并不属于if语句当中的而是属于第二个内循环当中的。**

循环语句支持` else `子句；`for `循环中，可迭代对象中的元素全部循环完毕，或` while `循环的条件为假时，执行该子句；`break` 语句终止循环时，不执行该子句。 `

与 if 语句相比，循环的 else 子句更像 try 的 else 子句： try 的 else 子句在未触发异常时执行，循环的 else 子句则在未运行 break 时执行。try 语句和异常详见 异常的处理。

### 3.5 pass 语句
pass语句仅表示意思是占位符的
```python
>>> while True:
...     pass  # Busy-wait for keyboard interrupt (Ctrl+C)
...
```
等同于
```C++
while(1)
{}
```
### 3.6 match语句
```python
def http_error(status):
    match status:
        case 400:
            return "Bad request"
        case 404:
            return "Not found"
        case 418:
            return "I'm a teapot"
        case _:
            return "Something's wrong with the internet"
```
* def 表示的是用户自定义函数
* 最后一个代码块：“变量名” _ 被作为`通配符`并必定会匹配成功。 如果没有`case`语句匹配成功，则不会执行任何分支。

使用 | （“ or ”）在一个模式中可以组合多个字面值：
```python
case 401 | 403 | 404:
    return "Not allowed"
```

### 3.7 定义函数
```python
>>> def fib(n):    # write Fibonacci series up to n
...     """Print a Fibonacci series up to n."""
...     a, b = 0, 1
...     while a < n:
...         print(a, end=' ')
...         a, b = b, a+b
...     print()
...
>>> # Now call the function we just defined:
... fib(2000)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597
```
定义 函数使用关键字 def，后跟函数名与括号内的形参列表。函数语句从下一行开始，**并且必须缩进**。
在c/c++当中空格所能有意义的用法非常稀少，但是python不用，应为没有中括号这类的符号标识，所以需要使用缩进表示的函数体

函数内的第一条语句是字符串时，该字符串就是文档字符串，也称为 docstring。**利用文档字符串可以自动生成在线文档或打印版文档，还可以让开发者在浏览代码时直接查阅文档**；Python 开发者最好养成在代码中加入文档字符串的好习惯。

函数在 执行 时使用函数局部变量符号表，所有函数变量赋值都存在局部符号表中；引用变量时，首先，在局部符号表里查找变量，然后，是外层函数局部符号表，再是全局符号表，最后是内置名称符号表。因此，尽管可以引用全局变量和外层函数的变量，但最好不要在函数内直接赋值（除非是 global 语句定义的全局变量，或 nonlocal 语句定义的外层函数变量）

在调用函数时会将实际参数（实参）引入到被调用函数的局部符号表中；因此，实参是使用 按值调用 来传递的（其中的 值 始终是对象的 引用 而不是对象的值）。 1 当一个函数调用另外一个函数时，会为该调用创建一个新的局部符号表。

函数定义在当前符号表中把函数名与函数对象关联在一起。解释器把函数名指向的对象作为用户自定义函数。还可以使用其他名称指向同一个函数对象，并访问访该函数：
```python
>>> fib
<function fib at 10042ed0>
>>> f = fib
>>> f(100)
0 1 1 2 3 5 8 13 21 34 55 89
```
函数返回值
```python
>>> def fib2(n):  # return Fibonacci series up to n
...     """Return a list containing the Fibonacci series up to n."""
...     result = []
...     a, b = 0, 1
...     while a < n:
...         result.append(a)    # see below
...         a, b = b, a+b
...     return result
...
>>> f100 = fib2(100)    # call it
>>> f100                # write the result
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
```

默认值在*定义*作用域里的函数定义中求值，所以
```python
i = 5

def f(arg=i):
    print(arg)

i = 6
f()
```
上例输出的是`5`。
**重要警告**： 默认值只计算一次。默认值为列表、字典或类实例等可变对象时，会产生与该规则不同的结果。例如，下面的函数会累积后续调用时传递的参数：
```python
def f(a, L=[]):
    L.append(a)
    return L

print(f(1))
print(f(2))
print(f(3))
```
输出结果如下：
```python
[1]
[1, 2]
[1, 2, 3]
```
不想在后续调用之间共享默认值时，应以如下方式编写函数：
```python
def f(a, L=None):
    if L is None:
        L = []
    L.append(a)
    return L
```


## 4.copy 浅层和深层复制操作
Python 的赋值语句不复制对象，而是创建目标和对象的绑定关系。对于自身可变，或包含可变项的集合，有时要生成副本用于改变操作，而不必改变原始对象。本模块提供了通用的浅层复制和深层复制操作，（如下所述）。
接口摘要：
copy.copy(x)
返回 x 的浅层复制。
copy.deepcopy(x[, memo])
返回 x 的深层复制。
exception copy.Error
针对模块特定错误引发。

上面也就是说明了list实际上表示的一个引用的关系，比如`alist = [1]`,那么`alist`本质上等于一个指针指向存储数据1的内存， `alist = blist`这个赋值关系，实际上就是等同于两个指针都指向同一片内存，如果任意修改一个`alist` / `blist` 这时候相当于这一个内存的内容发生变化，只要修改了一个两个list都会同时发生错误。这就是浅拷贝，浅拷贝就是内容之间存在依赖关系，具有相互影响的关系，在c语言的严重极端的情况下，如果要删除一个内存空间，另外一个指针本来只想的地方就消失，很容易造成sf问题（段错误问题），指针指向了一片不明空间，这样是十分危险的。
