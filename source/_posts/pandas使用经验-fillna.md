---
title: pandas使用经验-1
date: 2018-04-10 15:32:23
tags:
  - pandas
  - 数据处理
  - 数据清洗
categories:
  - pandas
  - 数据处理
---

函数原型：
https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.fillna.html#pandas.DataFrame.fillna

pad/ffill：用前一个非缺失值去填充该缺失值
backfill/bfill：用下一个非缺失值填充该缺失值
None:指定一个值去替换缺失值

```python
# coding: utf-8
import pandas as pd

df = pd.DataFrame([[1, None, 2],
                   [None, 3, None],
                   [None, 4, 5],])

print('origin')
print(df)
#      0    1    2
# 0  1.0  NaN  2.0
# 1  NaN  3.0  NaN
# 2  NaN  4.0  5.0

print('left')
data = df.bfill(axis=1).iloc[:, 0]
print(data)
# 0    1.0
# 1    3.0
# 2    4.0

print('up')
data = df.bfill().iloc[:, -1]
print(data)
# 0    2.0
# 1    5.0
# 2    5.0

print('left')
data = df.fillna(method='bfill',axis=1)
print(data)
#      0    1    2
# 0  1.0  2.0  2.0
# 1  3.0  3.0  NaN
# 2  4.0  4.0  5.0

print('up')
data = df.fillna(method='bfill')
print(data)
#      0    1    2
# 0  1.0  3.0  2.0
# 1  NaN  3.0  5.0
# 2  NaN  4.0  5.0

print('right')
data = df.fillna(method='ffill',axis=1)
print(data)
#      0    1    2
# 0  1.0  1.0  2.0
# 1  NaN  3.0  3.0
# 2  NaN  4.0  5.0

print('down')
data = df.fillna(method='ffill')
print(data)
#      0    1    2
# 0  1.0  NaN  2.0
# 1  1.0  3.0  2.0
# 2  1.0  4.0  5.0

print('left')
data = df.fillna(method='backfill',axis=1)
print(data)
#      0    1    2
# 0  1.0  2.0  2.0
# 1  3.0  3.0  NaN
# 2  4.0  4.0  5.0

print('up')
data = df.fillna(method='backfill')
print(data)
#      0    1    2
# 0  1.0  3.0  2.0
# 1  NaN  3.0  5.0
# 2  NaN  4.0  5.0

print('right')
data = df.fillna(method='pad',axis=1)
print(data)
#      0    1    2
# 0  1.0  1.0  2.0
# 1  NaN  3.0  3.0
# 2  NaN  4.0  5.0

print('down')
data = df.fillna(method='pad')
print(data)
#      0    1    2
# 0  1.0  NaN  2.0
# 1  1.0  3.0  2.0
# 2  1.0  4.0  5.0
```


```
import pandas as pd
import numpy as np

df = pd.DataFrame([[np.nan, 2, np.nan, 0],
                    [3, 4, np.nan, 1],
                        [np.nan, np.nan, np.nan, 5],
                        [np.nan, 3, np.nan, 4]],
                        columns = list('ABCD'))
print(df)
#      A    B   C  D
# 0  NaN  2.0 NaN  0
# 1  3.0  4.0 NaN  1
# 2  NaN  NaN NaN  5
# 3  NaN  3.0 NaN  4

print(df.fillna(0))
#      A    B    C  D
# 0  0.0  2.0  0.0  0
# 1  3.0  4.0  0.0  1
# 2  0.0  0.0  0.0  5
# 3  0.0  3.0  0.0  4

print(df.fillna(method='ffill'))
#      A    B   C  D
# 0  NaN  2.0 NaN  0
# 1  3.0  4.0 NaN  1
# 2  3.0  4.0 NaN  5
# 3  3.0  3.0 NaN  4

values = {'A': 0, 'B': 1, 'C': 2, 'D': 3}
print(df.fillna(value=values))
#      A    B    C  D
# 0  0.0  2.0  2.0  0
# 1  3.0  4.0  2.0  1
# 2  0.0  1.0  2.0  5
# 3  0.0  3.0  2.0  4

print(df.fillna(value=values, limit=1))
#      A    B    C  D
# 0  0.0  2.0  2.0  0
# 1  3.0  4.0  NaN  1
# 2  NaN  1.0  NaN  5
# 3  NaN  3.0  NaN  4
```