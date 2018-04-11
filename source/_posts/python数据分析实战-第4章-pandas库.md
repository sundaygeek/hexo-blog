---
title: python数据分析实战-第4章-pandas库
date: 2018-04-11 18:14:28
tags:
  - python
  - 数据分析
  - pandas
---


# 第4章　pandas库简介　　56
# 4.1　pandas：Python数据分析库　　56
# 4.2　安装　　57
## 4.2.1　用Anaconda安装　　57
## 4.2.2　用PyPI安装　　58
## 4.2.3　在Linux系统的安装方法　　58
## 4.2.4　用源代码安装　　58
## 4.2.5　Windows模块仓库　　59
# 4.3　测试pandas是否安装成功　　59
# 4.4　开始pandas之旅　　59


```python
import pandas as pd
import numpy as np
```

# 4.5　pandas数据结构简介　　60
## 4.5.1　Series对象　　60


```python
s = pd.Series([12,-4,7,9])
s
```




    0    12
    1    -4
    2     7
    3     9
    dtype: int64




```python
s = pd.Series([12,-4,7,9], index=['a','b','c','d'])
s
```




    a    12
    b    -4
    c     7
    d     9
    dtype: int64




```python
s.values
```




    array([12, -4,  7,  9])




```python
s.index
```




    Index(['a', 'b', 'c', 'd'], dtype='object')




```python
s[2], s['b']
```




    (7, -4)




```python
s[0:2]
```




    a    12
    b    -4
    dtype: int64




```python
s[['b','c']]
```




    b   -4
    c    7
    dtype: int64




```python
s[1] = 0
s
```




    a    12
    b     0
    c     7
    d     9
    dtype: int64




```python
s['b'] = 1
s
```




    a    12
    b     1
    c     7
    d     9
    dtype: int64




```python
arr = np.array([1,2,3,4])
s3 = pd.Series(arr)
s3
```




    0    1
    1    2
    2    3
    3    4
    dtype: int64




```python
s4 = pd.Series(s)
s4
```




    a    12
    b     1
    c     7
    d     9
    dtype: int64




```python
arr[2] = -2
s3
```




    0    1
    1    2
    2   -2
    3    4
    dtype: int64




```python
s[s > 8]
```




    a    12
    d     9
    dtype: int64




```python
s / 2
```




    a    6.0
    b    0.5
    c    3.5
    d    4.5
    dtype: float64




```python
np.log(s)
```




    a    2.484907
    b    0.000000
    c    1.945910
    d    2.197225
    dtype: float64




```python
serd = pd.Series([1,0,2,1,2,3], index=['white','white','blue','green','green','yellow'])
```


```python
serd
```




    white     1
    white     0
    blue      2
    green     1
    green     2
    yellow    3
    dtype: int64




```python
serd.unique()
```




    array([1, 0, 2, 3])




```python
serd.value_counts()
```




    2    2
    1    2
    3    1
    0    1
    dtype: int64




```python
serd.isin([0,3])
```




    white     False
    white      True
    blue      False
    green     False
    green     False
    yellow     True
    dtype: bool




```python
serd[serd.isin([0,3])]
```




    white     0
    yellow    3
    dtype: int64




```python
s2 = pd.Series([5,-3,np.NaN,14])
s2
```




    0     5.0
    1    -3.0
    2     NaN
    3    14.0
    dtype: float64




```python
s2.isnull()
```




    0    False
    1    False
    2     True
    3    False
    dtype: bool




```python
s2.notnull()
```




    0     True
    1     True
    2    False
    3     True
    dtype: bool




```python
s2[s2.notnull()]
```




    0     5.0
    1    -3.0
    3    14.0
    dtype: float64




```python
s2[s2.isnull()]
```




    2   NaN
    dtype: float64




```python
mydict = {'red': 2000, 'blue': 1000, 'yellow': 500, 'orange': 1000}
myseries = pd.Series(mydict)
myseries
```




    blue      1000
    orange    1000
    red       2000
    yellow     500
    dtype: int64




```python
colors = ['red','yellow','orange','blue','green']
myseries = pd.Series(mydict, index=colors)
myseries
```




    red       2000.0
    yellow     500.0
    orange    1000.0
    blue      1000.0
    green        NaN
    dtype: float64




```python
mydict2 = {'red':400,'yellow':1000,'black':700}
myseries2 = pd.Series(mydict2)
myseries2
```




    black      700
    red        400
    yellow    1000
    dtype: int64




```python
myseries + myseries2
```




    black        NaN
    blue         NaN
    green        NaN
    orange       NaN
    red       2400.0
    yellow    1500.0
    dtype: float64



## 4.5.2　DataFrame对象　　66


```python
data = {'color' : ['blue','green','yellow','red','white'],
'object' : ['ball','pen','pencil','paper','mug'],
'price' : [1.2,1.0,0.6,0.9,1.7]}
frame = pd.DataFrame(data)
frame
```

....|color|object|price
---- | --- | --- | ---
0	|blue	|ball	|1.2
1	|green	|pen	|1.0
2	|yellow	|pencil	|0.6
3	|red	|paper	|0.9
4	|white	|mug	|1.7



```python
frame2 = pd.DataFrame(data, columns=['object','price'])
frame2
```

..... |object|price
--- | --- | ---
0	|ball	|1.2
1	|pen	|1.0
2	|pencil	|0.6
3	|paper	|0.9
4	|mug	|1.7




```python
frame2 = pd.DataFrame(data, index=['one','two','three','four','five'])
frame2
```

....|color	|object	|price
---- | --- | --- | ---
one	|blue	|ball	|1.2
two	|green	|pen	|1.0
three	|yellow	|pencil	|0.6
four	|red	|paper	|0.9
five	|white	|mug	|1.7




```python
frame3 = pd.DataFrame(np.arange(16).reshape((4,4)),
    index=['red','blue','yellow','white'],
    columns=['ball','pen','pencil','paper'])
frame3
```

.....|ball	|pen	|pencil	|paper
---- | --- | --- | --- | ----
red	|0	|1	|2	|3
blue	|4	|5	|6	|7
yellow	|8	|9	|10	|11
white	|12	|13	|14	|15




```python
frame.columns
```




    Index(['color', 'object', 'price'], dtype='object')




```python
frame.index
```




    RangeIndex(start=0, stop=5, step=1)




```python
frame.values
```




    array([['blue', 'ball', 1.2],
           ['green', 'pen', 1.0],
           ['yellow', 'pencil', 0.6],
           ['red', 'paper', 0.9],
           ['white', 'mug', 1.7]], dtype=object)




```python
frame['price']
```




    0    1.2
    1    1.0
    2    0.6
    3    0.9
    4    1.7
    Name: price, dtype: float64




```python
frame.price
```




    0    1.2
    1    1.0
    2    0.6
    3    0.9
    4    1.7
    Name: price, dtype: float64




```python
frame.ix[2]
```




    color     yellow
    object    pencil
    price        0.6
    Name: 2, dtype: object




```python
frame.ix[[2,4]]
```





```python
frame[0:1]
```

	color	object	price
0	blue	ball	1.2



```python
frame[1:3]
```


	color	object	price
1	green	pen	1.0
2	yellow	pencil	0.6


```python
frame['object'][3]
```




    'paper'




```python
frame.index.name = 'id'; 
frame.columns.name = 'item'
frame
```


item	color	object	price
id			
0	blue	ball	1.2
1	green	pen	1.0
2	yellow	pencil	0.6
3	red	paper	0.9
4	white	mug	1.7


```python
frame['new'] = 12
frame
```


item	color	object	price	new
id				
0	blue	ball	1.2	12
1	green	pen	1.0	12
2	yellow	pencil	0.6	12
3	red	paper	0.9	12
4	white	mug	1.7	12

```python
frame['new'] = [3.0,1.3,2.2,0.8,1.1]
frame
```


item	color	object	price	new
id				
0	blue	ball	1.2	3.0
1	green	pen	1.0	1.3
2	yellow	pencil	0.6	2.2
3	red	paper	0.9	0.8
4	white	mug	1.7	1.1


```python
ser = pd.Series(np.arange(5))
ser
```




    0    0
    1    1
    2    2
    3    3
    4    4
    dtype: int64




```python
frame['new'] = ser
frame
```

item	color	object	price	new
id				
0	blue	ball	1.2	0
1	green	pen	1.0	1
2	yellow	pencil	0.6	2
3	red	paper	0.9	3
4	white	mug	1.7	4


```python
frame['price'][2] = 3.3
frame
```

    /home/vivoadmin/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:1: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      """Entry point for launching an IPython kernel.



item	color	object	price	new
id				
0	blue	ball	1.2	0
1	green	pen	1.0	1
2	yellow	pencil	3.3	2
3	red	paper	0.9	3
4	white	mug	1.7	4


```python
frame.isin([1.0,'pen'])
```


item	color	object	price	new
id				
0	False	False	False	False
1	False	True	True	True
2	False	False	False	False
3	False	False	False	False
4	False	False	False	False


```python
frame[frame.isin([1.0,'pen'])]
```


item	color	object	price	new
id				
0	NaN	NaN	NaN	NaN
1	NaN	pen	1.0	1.0
2	NaN	NaN	NaN	NaN
3	NaN	NaN	NaN	NaN
4	NaN	NaN	NaN	NaN



```python
del frame['new']
frame
```

item	color	object	price
id			
0	blue	ball	1.2
1	green	pen	1.0
2	yellow	pencil	3.3
3	red	paper	0.9
4	white	mug	1.7


```python
frame[frame < 1]
```


item	color	object	price
id			
0	blue	ball	NaN
1	green	pen	NaN
2	yellow	pencil	NaN
3	red	paper	0.9
4	white	mug	NaN


```python
nestdict = {'red':{2012: 22, 2013: 33},
    'white':{2011: 13, 2012: 22, 2013: 16},
    'blue': {2011: 17, 2012: 27, 2013: 18}}
frame2 = pd.DataFrame(nestdict)
frame2
```

	blue	red	white
2011	17	NaN	13
2012	27	22.0	22
2013	18	33.0	16


```python
frame2.T
```


	2011	2012	2013
blue	17.0	27.0	18.0
red	NaN	22.0	33.0
white	13.0	22.0	16.0


## 4.5.3　Index对象　　72


```python
ser = pd.Series([5,0,3,8,4], index=['red','blue','yellow','white','green'])
ser.index
```




    Index(['red', 'blue', 'yellow', 'white', 'green'], dtype='object')




```python
ser.idxmin(), ser.idxmax()
```




    ('blue', 'white')




```python
serd = pd.Series(range(6), index=['white','white','blue','green','green','yellow'])
serd
```




    white     0
    white     1
    blue      2
    green     3
    green     4
    yellow    5
    dtype: int64




```python
serd['white']
```




    white    0
    white    1
    dtype: int64




```python
serd.index.is_unique
```




    False




```python
frame.index.is_unique
```




    True



# 4.6　索引对象的其他功能　　74
## 4.6.1　更换索引　　74


```python
ser = pd.Series([2,5,7,4], index=['one','two','three','four'])
ser
```




    one      2
    two      5
    three    7
    four     4
    dtype: int64




```python
ser.reindex(['three','four','five','one'])
```




    three    7.0
    four     4.0
    five     NaN
    one      2.0
    dtype: float64




```python
ser3 = pd.Series([1,5,6,3],index=[0,3,5,6])
ser3
```




    0    1
    3    5
    5    6
    6    3
    dtype: int64




```python
ser3.reindex(range(6),method='ffill')
```




    0    1
    1    1
    2    1
    3    5
    4    5
    5    6
    dtype: int64




```python
ser3.reindex(range(6),method='bfill')
```




    0    1
    1    5
    2    5
    3    5
    4    6
    5    6
    dtype: int64


item	colors	price	new	object
id				
0	blue	1.2	blue	ball
1	green	1.0	green	pen
2	yellow	3.3	yellow	pencil
3	red	0.9	red	paper
4	white	1.7	white	mug


## 4.6.2　删除　　75


```python
ser = pd.Series(np.arange(4.), index=['red','blue','yellow','white'])
ser
```




    red       0.0
    blue      1.0
    yellow    2.0
    white     3.0
    dtype: float64




```python
ser.drop('yellow')
```




    red      0.0
    blue     1.0
    white    3.0
    dtype: float64




```python
ser.drop(['blue','white'])
```




    red       0.0
    yellow    2.0
    dtype: float64




```python
frame = pd.DataFrame(np.arange(16).reshape((4,4)),
    index=['red','blue','yellow','white'],
    columns=['ball','pen','pencil','paper'])
frame
```


	ball	pen	pencil	paper
red	0	1	2	3
blue	4	5	6	7
yellow	8	9	10	11
white	12	13	14	15

```python
frame.drop(['blue','yellow'])
```



ball	pen	pencil	paper
red	0	1	2	3
white	12	13	14	15



```python
frame.drop(['pen','pencil'],axis=1)
```


	ball	paper
red	0	3
blue	4	7
yellow	8	11
white	12	15


## 4.6.3　算术和数据对齐　　77


```python
s1 = pd.Series([3,2,5,1],['white','yellow','green','blue'])
s2 = pd.Series([1,4,7,2,1],['white','yellow','black','blue','brown'])
```


```python
s1 + s2
```




    black     NaN
    blue      3.0
    brown     NaN
    green     NaN
    white     4.0
    yellow    6.0
    dtype: float64




```python
frame1 = pd.DataFrame(np.arange(16).reshape((4,4)),
    index=['red','blue','yellow','white'],
    columns=['ball','pen','pencil','paper'])
frame2 = pd.DataFrame(np.arange(12).reshape((4,3)),
    index=['blue','green','white','yellow'],
    columns=['mug','pen','ball'])

```


```python
frame1
```


	ball	pen	pencil	paper
red	0	1	2	3
blue	4	5	6	7
yellow	8	9	10	11
white	12	13	14	15


```python
frame2
```


	mug	pen	ball
blue	0	1	2
green	3	4	5
white	6	7	8
yellow	9	10	11

```python
frame1 + frame2
```


	ball	mug	paper	pen	pencil
blue	6.0	NaN	NaN	6.0	NaN
green	NaN	NaN	NaN	NaN	NaN
red	NaN	NaN	NaN	NaN	NaN
white	20.0	NaN	NaN	20.0	NaN
yellow	19.0	NaN	NaN	19.0	NaN


# 4.7　数据结构之间的运算　　78
## 4.7.1　灵活的算术运算方法　　78


```python
frame1.add(frame2)
```


	ball	mug	paper	pen	pencil
blue	6.0	NaN	NaN	6.0	NaN
green	NaN	NaN	NaN	NaN	NaN
red	NaN	NaN	NaN	NaN	NaN
white	20.0	NaN	NaN	20.0	NaN
yellow	19.0	NaN	NaN	19.0	NaN



## 4.7.2　DataFrame和Series对象之间的运算　　78


```python
frame = pd.DataFrame(np.arange(16).reshape((4,4)),
    index=['red','blue','yellow','white'],
    columns=['ball','pen','pencil','paper'])
frame
```



	ball	pen	pencil	paper
red	0	1	2	3
blue	4	5	6	7
yellow	8	9	10	11
white	12	13	14	15



```python
ser = pd.Series(np.arange(4), index=['ball','pen','pencil','paper'])
ser
```




    ball      0
    pen       1
    pencil    2
    paper     3
    dtype: int64




```python
frame - ser
```



	ball	pen	pencil	paper
red	0	0	0	0
blue	4	4	4	4
yellow	8	8	8	8
white	12	12	12	12



```python
ser['mug'] = 9
ser
```




    ball      0
    pen       1
    pencil    2
    paper     3
    mug       9
    dtype: int64




```python
frame - ser
```


	ball	mug	paper	pen	pencil
red	0	NaN	0	0	0
blue	4	NaN	4	4	4
yellow	8	NaN	8	8	8
white	12	NaN	12	12	12

# 4.8　函数应用和映射　　79
## 4.8.1　操作元素的函数　　79


```python
frame = pd.DataFrame(np.arange(16).reshape((4,4)),
    index=['red','blue','yellow','white'],
    columns=['ball','pen','pencil','paper'])
frame
```


	ball	pen	pencil	paper
red	0	1	2	3
blue	4	5	6	7
yellow	8	9	10	11
white	12	13	14	15


```python
np.sqrt(frame)
```


	ball	pen	pencil	paper
red	0.000000	1.000000	1.414214	1.732051
blue	2.000000	2.236068	2.449490	2.645751
yellow	2.828427	3.000000	3.162278	3.316625
white	3.464102	3.605551	3.741657	3.872983

## 4.8.2　按行或列执行操作的函数　　80


```python
f = lambda x: x.max() - x.min()
def f(x):
    return x.max() - x.min()
```


```python
frame.apply(f)
```




    ball      12
    pen       12
    pencil    12
    paper     12
    dtype: int64




```python
frame.apply(f, axis=1)
```




    red       3
    blue      3
    yellow    3
    white     3
    dtype: int64




```python
def f(x):
    return pd.Series([x.min(), x.max()], index=['min','max'])
```


```python
frame.apply(f)
```

	ball	pen	pencil	paper
min	0	1	2	3
max	12	13	14	15

## 4.8.3　统计函数　　81


```python
frame.sum()
```




    ball      24
    pen       28
    pencil    32
    paper     36
    dtype: int64




```python
frame.mean()
```




    ball      6.0
    pen       7.0
    pencil    8.0
    paper     9.0
    dtype: float64




```python
frame.describe()
```


	ball	pen	pencil	paper
count	4.000000	4.000000	4.000000	4.000000
mean	6.000000	7.000000	8.000000	9.000000
std	5.163978	5.163978	5.163978	5.163978
min	0.000000	1.000000	2.000000	3.000000
25%	3.000000	4.000000	5.000000	6.000000
50%	6.000000	7.000000	8.000000	9.000000
75%	9.000000	10.000000	11.000000	12.000000
max	12.000000	13.000000	14.000000	15.000000

# 4.9　排序和排位次　　81


```python
ser = pd.Series([5,0,3,8,4], index=['red','blue','yellow','white','green'])
ser
```




    red       5
    blue      0
    yellow    3
    white     8
    green     4
    dtype: int64




```python
ser.sort_index()
```




    blue      0
    green     4
    red       5
    white     8
    yellow    3
    dtype: int64




```python
ser.sort_index(ascending=False)
```




    yellow    3
    white     8
    red       5
    green     4
    blue      0
    dtype: int64




```python
frame = pd.DataFrame(np.arange(16).reshape((4,4)),
    index=['red','blue','yellow','white'],
    columns=['ball','pen','pencil','paper'])
frame
```


	ball	pen	pencil	paper
red	0	1	2	3
blue	4	5	6	7
yellow	8	9	10	11
white	12	13	14	15

```python
frame.sort_index()
```


	ball	pen	pencil	paper
blue	4	5	6	7
red	0	1	2	3
white	12	13	14	15
yellow	8	9	10	11


```python
frame.sort_index(axis=1)
```


	ball	paper	pen	pencil
red	0	3	1	2
blue	4	7	5	6
yellow	8	11	9	10
white	12	15	13	14

```python
ser.order()
```


    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-131-bb828ac4164f> in <module>()
    ----> 1 ser.order()
    

    ~/anaconda3/lib/python3.6/site-packages/pandas/core/generic.py in __getattr__(self, name)
       3612             if name in self._info_axis:
       3613                 return self[name]
    -> 3614             return object.__getattribute__(self, name)
       3615 
       3616     def __setattr__(self, name, value):


    AttributeError: 'Series' object has no attribute 'order'



```python
frame.sort_index(by='pen')
```

    /home/vivoadmin/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:1: FutureWarning: by argument to sort_index is deprecated, please use .sort_values(by=...)
      """Entry point for launching an IPython kernel.



	ball	pen	pencil	paper
red	0	1	2	3
blue	4	5	6	7
yellow	8	9	10	11
white	12	13	14	15


```python
frame.sort_index(by=['pen','pencil'])
```

    /home/vivoadmin/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:1: FutureWarning: by argument to sort_index is deprecated, please use .sort_values(by=...)
      """Entry point for launching an IPython kernel.




ball	pen	pencil	paper
red	0	1	2	3
blue	4	5	6	7
yellow	8	9	10	11
white	12	13	14	15

```python
ser.rank()
```




    red       4.0
    blue      1.0
    yellow    2.0
    white     5.0
    green     3.0
    dtype: float64




```python
ser.rank(method='first')
```




    red       4.0
    blue      1.0
    yellow    2.0
    white     5.0
    green     3.0
    dtype: float64




```python
ser.rank(ascending=False)
```




    red       2.0
    blue      5.0
    yellow    4.0
    white     1.0
    green     3.0
    dtype: float64



# 4.10　相关性和协方差　　84


```python
seq2 = pd.Series([3,4,3,4,5,4,3,2],['2006','2007','2008','2009','2010','2011','2012','2013'])
seq = pd.Series([1,2,3,4,4,3,2,1],['2006','2007','2008','2009','2010','2011','2012','2013'])
```


```python
seq.corr(seq2)
```




    0.7745966692414835




```python
seq.cov(seq2)
```




    0.8571428571428571




```python
frame2 = pd.DataFrame([[1,4,3,6],[4,5,6,1],[3,3,1,5],[4,1,6,4]],
    index=['red','blue','yellow','white'],
    columns=['ball','pen','pencil','paper'])
frame2
```

	ball	pen	pencil	paper
red	1	4	3	6
blue	4	5	6	1
yellow	3	3	1	5
white	4	1	6	4

```python
frame2.corr()
```


	ball	pen	pencil	paper
ball	1.000000	-0.276026	0.577350	-0.763763
pen	-0.276026	1.000000	-0.079682	-0.361403
pencil	0.577350	-0.079682	1.000000	-0.692935
paper	-0.763763	-0.361403	-0.692935	1.000000



```python
frame2.cov()
```


	ball	pen	pencil	paper
ball	2.000000	-0.666667	2.000000	-2.333333
pen	-0.666667	2.916667	-0.333333	-1.333333
pencil	2.000000	-0.333333	6.000000	-3.666667
paper	-2.333333	-1.333333	-3.666667	4.666667

```python
ser
```




    red       5
    blue      0
    yellow    3
    white     8
    green     4
    dtype: int64




```python
frame2.corrwith(ser)
```




    ball     -0.140028
    pen      -0.869657
    pencil    0.080845
    paper     0.595854
    dtype: float64




```python
frame2.corrwith(frame)
```




    ball      0.730297
    pen      -0.831522
    pencil    0.210819
    paper    -0.119523
    dtype: float64



# 4.11　NaN数据　　85
## 4.11.1　为元素赋NaN值　　85


```python
ser = pd.Series([0,1,2,np.NaN,9], index=['red','blue','yellow','white','green'])
ser
```




    red       0.0
    blue      1.0
    yellow    2.0
    white     NaN
    green     9.0
    dtype: float64




```python
ser['white'] = None
ser
```




    red       0.0
    blue      1.0
    yellow    2.0
    white     NaN
    green     9.0
    dtype: float64



## 4.11.2　过滤NaN　　86


```python
ser.dropna()
```




    red       0.0
    blue      1.0
    yellow    2.0
    green     9.0
    dtype: float64




```python
ser[ser.notnull()]
```




    red       0.0
    blue      1.0
    yellow    2.0
    green     9.0
    dtype: float64




```python
frame3 = pd.DataFrame([[6,np.nan,6],[np.nan,np.nan,np.nan],[2,np.nan,5]],
    index = ['blue','green','red'],
    columns = ['ball','mug','pen'])
frame3
```


	ball	mug	pen
blue	6.0	NaN	6.0
green	NaN	NaN	NaN
red	2.0	NaN	5.0


```python
frame3.dropna()
```


ball	mug	pen


```python
frame3.dropna(how='all')
```


	ball	mug	pen
blue	6.0	NaN	6.0
red	2.0	NaN	5.0



## 4.11.3　为NaN元素填充其他值　　86


```python
frame3.fillna(0)
```


	ball	mug	pen
blue	6.0	0.0	6.0
green	0.0	0.0	0.0
red	2.0	0.0	5.0



```python
frame3.fillna({'ball':1,'mug':0,'pen':99})
```


	ball	mug	pen
blue	6.0	0.0	6.0
green	1.0	0.0	99.0
red	2.0	0.0	5.0


# 4.12　等级索引和分级　　87


```python
mser = pd.Series(np.random.rand(8),
    index=[['white','white','white','blue','blue','red','red','red'],
    ['up','down','right','up','down','up','down','left']])
mser
```




    white  up       0.941438
           down     0.165728
           right    0.492898
    blue   up       0.902410
           down     0.879385
    red    up       0.646074
           down     0.210103
           left     0.476333
    dtype: float64




```python
mser.index
```




    MultiIndex(levels=[['blue', 'red', 'white'], ['down', 'left', 'right', 'up']],
               labels=[[2, 2, 2, 0, 0, 1, 1, 1], [3, 0, 2, 3, 0, 3, 0, 1]])




```python
mser['white']
```




    up       0.941438
    down     0.165728
    right    0.492898
    dtype: float64




```python
mser[:,'up']
```




    white    0.941438
    blue     0.902410
    red      0.646074
    dtype: float64




```python
mser['white','up']
```




    0.9414384939430696




```python
mser.unstack()
```

	down	left	right	up
blue	0.879385	NaN	NaN	0.902410
red	0.210103	0.476333	NaN	0.646074
white	0.165728	NaN	0.492898	0.941438



```python
frame
```



	ball	pen	pencil	paper
red	0	1	2	3
blue	4	5	6	7
yellow	8	9	10	11
white	12	13	14	15


```python
frame.stack()
```




    red     ball       0
            pen        1
            pencil     2
            paper      3
    blue    ball       4
            pen        5
            pencil     6
            paper      7
    yellow  ball       8
            pen        9
            pencil    10
            paper     11
    white   ball      12
            pen       13
            pencil    14
            paper     15
    dtype: int64




```python
mframe = pd.DataFrame(np.random.randn(16).reshape(4,4),
    index=[['white','white','red','red'], ['up','down','up','down']],
    columns=[['pen','pen','paper','paper'],[1,2,1,2]])
mframe
```


		pen	paper
1	2	1	2
white	up	0.992202	-1.037634	-0.083882	-0.028204
down	-1.758579	1.832742	-0.167739	0.174567
red	up	0.983876	2.306897	-1.003258	-0.591767
down	1.398166	0.054964	-1.705692	-0.375007


## 4.12.1　重新调整顺序和为层级排序　　89


```python
mframe.columns.names = ['objects','id']
mframe.index.names = ['colors','status']
mframe
```


	objects	pen	paper
id	1	2	1	2
colors	status				
white	up	0.992202	-1.037634	-0.083882	-0.028204
down	-1.758579	1.832742	-0.167739	0.174567
red	up	0.983876	2.306897	-1.003258	-0.591767
down	1.398166	0.054964	-1.705692	-0.375007



```python
mframe.swaplevel('colors','status')
```


	objects	pen	paper
id	1	2	1	2
status	colors				
up	white	0.992202	-1.037634	-0.083882	-0.028204
down	white	-1.758579	1.832742	-0.167739	0.174567
up	red	0.983876	2.306897	-1.003258	-0.591767
down	red	1.398166	0.054964	-1.705692	-0.375007

```python
mframe.sortlevel('colors')
```

    /home/vivoadmin/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:1: FutureWarning: sortlevel is deprecated, use sort_index(level= ...)
      """Entry point for launching an IPython kernel.



	objects	pen	paper
id	1	2	1	2
colors	status				
red	down	1.398166	0.054964	-1.705692	-0.375007
up	0.983876	2.306897	-1.003258	-0.591767
white	down	-1.758579	1.832742	-0.167739	0.174567
up	0.992202	-1.037634	-0.083882	-0.028204

## 4.12.2　按层级统计数据　　89


```python
mframe.sum(level='colors')
```


objects	pen	paper
id	1	2	1	2
colors				
white	-0.766377	0.795107	-0.251622	0.146363
red	2.382042	2.361861	-2.708950	-0.966774

```python
mframe.sum(level='id', axis=1)
```



	id	1	2
colors	status		
white	up	0.908320	-1.065838
down	-1.926319	2.007309
red	up	-0.019382	1.715131
down	-0.307526	-0.320044

# 4.13　小结　　90
