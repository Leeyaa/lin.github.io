---
title: python 初长成
date: 2018-6-16 09：55：22
categories:
- python
---

刚刚开始学习python，多多指教

<!-- more -->



## 输入输出



### print()会依次打印每个字符串，遇到逗号“,”会输出一个空格



>print('The quick brown fox', 'jumps over', 'the lazy dog')



### input()输入参数为str类型，如果需要整数型，需要转换

>     a_str = input()

>     a_int = int(a)



* Q1 数组输入？



## 代码格式



### 每一行都是一个语句，当语句以冒号:结尾时，*缩进的语句视为代码块*。



>     # print absolute value of an integer:

>     a = 100

>	  if a >= 0:

>     	print(a)

>     else:

>     	print(-a)







#### Python程序是大小写敏感的



## 转义



### 1)如果字符串内部既包含'又包含"怎么办？可以用转义字符\\来标识

>print('I\'m ok.')



### 2)Python还允许用r''表示''内部的字符串默认不转义

>     >>> print(r'\\\t\\')

>     \\\t\\



---



### 为了简化，Python允许用'''...'''的格式表示多行内容

>     print('''line1

>     line2

>     line3''')

>

>     line1

>     line2

>     line3



## 布尔值

### 在Python中，可以直接用True、False表示布尔值

>     >>> 3 > 2

>     True

>     >>> 3 > 5

>     False





### 布尔值可以用and、or和not运算

### 动态语言/静态语言



>变量本身类型不固定的语言称之为动态语言



## 除法/ 和 //

### 1)  / 除法计算结果是浮点数，即使是两个整数恰好整除，结果也是浮点数



### 2)  //称为地板除，两个整数的除法仍然是整数

---



### Python的整数没有大小限制



## 数组list tuple

### 1)list

### * 如果要取最后一个元素，除了计算索引位置外，还可以用-1做索引，直接获取最后一个元素



### 2)tuple

### * tuple一旦初始化就不能修改



### 数组/字符串切片

1)后10个数：

>     >>> L[-10:]

>     [90, 91, 92, 93, 94, 95, 96, 97, 98, 99]

2)取前3个元素

>     >>> L[0:3]

>     ['Michael', 'Sarah', 'Tracy']

3）前10个数，每两个取一个：

>     >>> L[:10:2]

>     [0, 2, 4, 6, 8]

4)字符串'xxx'也可以看成是一种list

>     >>> 'ABCDEFG'[:3]

>     'ABC'

>     >>> 'ABCDEFG'[::2]

>     'ACEG'





---



## IF判断

### if语句执行有个特点，它是从上往下判断，如果在某个判断上是True，把该判断对应的语句执行后，就忽略掉剩下的elif和else

>     age = 20

>     if age >= 6:

>         print('teenager')

>     elif age >= 18:

>         print('adult')

>     else:

>         print('kid')



## For循环迭代

### 能否迭代判断，通过collections模块的Iterable类型判断：

>     >>> from collections import Iterable

>     >>> isinstance('abc', Iterable) # str是否可迭代

>     True

>     >>> isinstance([1,2,3], Iterable) # list是否可迭代

>     True

>     >>> isinstance(123, Iterable) # 整数是否可迭代

>     False



### 双参数迭代

>     >>> for x, y in [(1, 1), (2, 4), (3, 9)]:

>     ...     print(x, y)

>     ...

>     1 1

>     2 4

>     3 9

---





## python 常用库



#### numpy , matplotlib , pandas , xlwt , xlrd , 



---

## 函数

### 类型转换函数：hex,int,str,bool,float



### 数据类型检查函数:isinstance()

>     def my_abs(x):

>         if not isinstance(x, (int, float)):

>             raise TypeError('bad operand type')

>         if x >= 0:

>             return x

>         else:

>             return -x



### 返回多个值：



>     import math

>     

>     def move(x, y, step, angle=0):

>         nx = x + step * math.cos(angle)

>         ny = y - step * math.sin(angle)

>         return nx, ny





>     >>> x, y = move(100, 100, 60, math.pi / 6)

>     >>> print(x, y)

>     151.96152422706632 70.0

#### 其实这只是一种假象，Python函数返回的仍然是单一值。

>     >>> r = move(100, 100, 60, math.pi / 6)

>     >>> print(r)

>     (151.96152422706632, 70.0)



### 默认参数



当我们调用power(5)时，相当于调用power(5, 2)

> def power(x, n=2):



### 好处：

大多数学生注册时不需要提供年龄和城市，只提供必须的两个参数：

> def enroll(name, gender, age=6, city='Beijing'):



>     enroll('Bob', 'M', 7)

>     enroll('Adam', 'M', city='Tianjin')



### 默认参数必须指向不变对象！

>     def add_end(L=[]):

>         L.append('END')

>         return L



>     >>> add_end()

>     ['END']

>     >>> add_end()

>     ['END', 'END']

### 避免错误：L指向None,不变对象

>     def add_end(L=None):

>         if L is None:

>             L = []

>         L.append('END')

>         return L



### 可变参数

>     def calc(numbers):

>         sum = 0

>         for n in numbers:

>             sum = sum + n * n

>         return sum



>     >>> calc([1, 2, 3])

>     14

>     >>> calc((1, 3, 5, 7))

>     84

    

    

### 以Python允许你在list或tuple前面加一个*号，把list或tuple的元素变成可变参数传进去：

    

>     def calc(*numbers):

>         sum = 0

>         for n in numbers:

>             sum = sum + n * n

>         return sum





>     >>> calc(1, 2)

>     5

>     >>> calc()

>     0





>     >>> nums = [1, 2, 3]

>     >>> calc(*nums)

>     14





### 关键字参数 && 命名关键字参数



## 列表生成式

建立类似于x个x*x的list





>[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]



从XYZ与ABC两组数组组合全排列的

>     >>>[m + n for m in 'ABC' for n in 'XYZ']

>     ['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']



## 字符串操作

### 检查是否为字符串

>     >>> x = 'abc'

>     >>> y = 123

>     >>> isinstance(x, str)

>     True

>     >>> isinstance(y, str)

>     False



### 字符串大写变小写s.lower()

>     >>> L = ['Hello', 'World', 'IBM', 'Apple']

>     >>> [s.lower() for s in L]

>     ['hello', 'world', 'ibm', 'apple']