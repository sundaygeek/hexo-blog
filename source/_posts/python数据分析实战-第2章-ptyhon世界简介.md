---
title: python数据分析实战-第2章-ptyhon世界简介
date: 2018-04-11 16:22:56
tags:
  - 数据分析
  - python
categories:
  - 数据分析
  - python
---

这一章主要是回顾一些python的知识点。

第2章　Python世界简介　　12
2.1　Python——编程语言　　12
2.2　Python——解释器　　13
2.2.1　Cython　　14
2.2.2　Jython　　14
2.2.3　PyPy　　14
2.3　Python 2和Python 3　　14
2.4　安装Python　　15
2.5　Python发行版　　15
2.5.1　Anaconda　　15
2.5.2　Enthought Canopy　　16
2.5.3　Python(x,y)　　17
2.6　使用Python　　17
2.6.1　Python shell　　17
2.6.2　运行完整的Python程序　　17
2.6.3　使用IDE编写代码　　18
2.6.4　跟Python交互　　18
2.7　编写Python代码　　18
2.7.1　数学运算　　18
```
>>> 1 + 2
3
>>> (1.045 * 3)/4
0.78375
>>> 4 ** 2
16
>>> ((4 + 5j) * (2 + 3j))
(-7+22j)
>>> 4 < (2*3)
True
>>> a = 12 * 3.4
>>> a
40.8
```
2.7.2　导入新的库和函数　　19
```
>>> import math
>>> math.sin(a)
>>> from math import *
>>> sin(a)
>>> from math import sin

>>> dict = {'name':'William', 'age':25, 'city':'London'}
>>> dict["name"]
'William'
>>> for key, value in dict.items():
... print(key,value)
...
name William
city London
age 25

>>> list = [1,2,3,4]
>>> list
[1, 2, 3, 4]

>>> list[2]
3
>>> list[1:3]
[2, 3]
>>> list[-1]
4

>>> items = [1,2,3,4,5]
>>> for item in items:
... item + 1
...
23456

```
2.7.3　函数式编程　　21
```
>>> items = [1,2,3,4,5]
>>> def inc(x): return x+1
...
>>> list(map(inc,items))
[2, 3, 4, 5, 6]

>>> list(map((lambda x: x+1),items))
[2, 3, 4, 5, 6]

filter函数只抽取函数返回结果为True的列表元素。
reduce函数对列表中的而所有元素依次计算过后返回唯一结果。
>>> list(filter((lambda x: x < 4), items))
[1, 2, 3]
>>> from functools import reduce
>>> reduce((lambda x,y: x/y), items)
0.008333333333333333

>>> S = [x**2 for x in range(5)]
>>> S
[0, 1, 4, 9, 16]
```
2.7.4　缩进　　22
```
>>> a = 4
>>> if a > 3:
... if a < 5:
... print("I'm four")
... else:
... print("I'm a little number")
...
I'm four
>>> if a > 3:
... if a < 5:
... print("I'm four")
... else:
... print("I'm a big number")
...
I'm four

```
2.8　IPython　　23
2.8.1　IPython shell　　23
2.8.2　IPython Qt-Console　　24
2.9　PyPI仓库——Python包索引　　25
2.10　多种Python IDE　　26
2.10.1　IDLE　　26
2.10.2　Spyder　　27
2.10.3　Eclipse（pyDev）　　27
2.10.4　Sublime　　28
2.10.5　Liclipse　　29
2.10.6　NinjaIDE　　29
2.10.7　Komodo IDE　　29
2.11　SciPy　　30
2.11.1　NumPy　　30
2.11.2　pandas　　30
2.11.3　matplotlib　　31
2.12　小结　　31
