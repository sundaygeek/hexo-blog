---
title: python-zip函数使用
date: 2018-04-12 11:44:07
tags:
  - python
  - zip
---


>>> zip((1,2,3),(10,20,30),(100,200,300))
[(1, 10, 100), (2, 20, 200), (3, 30, 300)]

>>> [sum(x) for x in zip((1,2,3),(10,20,30),(100,200,300))]
[111, 222, 333]
To do this with an arbitrarily large set of tuples:

>>> myTuples = [(1,2,3), (10,20,30), (100,200,300)]
>>> [sum(x) for x in zip(*myTuples)]
[111, 222, 333]
sidenote: in python3, note that zip returns a lazy iterable, which you can always explicitly turn into a list like any other kind of iterable: list(zip(...))
