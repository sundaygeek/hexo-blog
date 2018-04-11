---
title: python数据分析实战-第3章-numpy库
date: 2018-04-11 16:46:53
tags:
  - python
  - numpy
  - 数据分析
---


# 第3章　NumPy库　　32
# 3.1　NumPy简史　　32
# 3.2　NumPy安装　　32
# 3.3　ndarray：NumPy库的心脏　　33


```python
import numpy as np
```


```python
a = np.array([1, 2, 3])
```


```python
a
```




    array([1, 2, 3])




```python
type(a), a.dtype, a.ndim, a.size, a.shape, a.itemsize
```




    (numpy.ndarray, dtype('int64'), 1, 3, (3,), 8)




```python
b = np.array([[1.3, 2.4],[0.3, 4.1]])
```


```python
b
```




    array([[1.3, 2.4],
           [0.3, 4.1]])




```python
type(b), b.dtype, b.ndim, b.size, b.shape, b.itemsize
```




    (numpy.ndarray, dtype('float64'), 2, 4, (2, 2), 8)




```python
b.data, type(b.data)
```




    (<memory at 0x7f3df8064558>, memoryview)



# 3.3.1　创建数组　　34


```python
c = np.array([[1, 2, 3],[4, 5, 6]])
```


```python
c
```




    array([[1, 2, 3],
           [4, 5, 6]])




```python
d = np.array(((1, 2, 3),(4, 5, 6)))
```


```python
d
```




    array([[1, 2, 3],
           [4, 5, 6]])




```python
e = np.array([(1, 2, 3), [4, 5, 6], (7, 8, 9)])
```


```python
e
```




    array([[1, 2, 3],
           [4, 5, 6],
           [7, 8, 9]])



# 3.3.2　数据类型　　34


```python
g = np.array([['a', 'b'],['c', 'd']])
```


```python
g
```




    array([['a', 'b'],
           ['c', 'd']], dtype='<U1')




```python
g.dtype, g.dtype.name
```




    (dtype('<U1'), 'str32')



# 3.3.3　dtype选项　　35


```python
f = np.array([[1, 2, 3],[4, 5, 6]], dtype=complex)
```


```python
f
```




    array([[1.+0.j, 2.+0.j, 3.+0.j],
           [4.+0.j, 5.+0.j, 6.+0.j]])



# 3.3.4　自带的数组创建方法　　36


```python
np.zeros((3, 3))
```




    array([[0., 0., 0.],
           [0., 0., 0.],
           [0., 0., 0.]])




```python
np.ones((3, 3))
```




    array([[1., 1., 1.],
           [1., 1., 1.],
           [1., 1., 1.]])




```python
np.arange(0, 10)
```




    array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])




```python
np.arange(4, 10)
```




    array([4, 5, 6, 7, 8, 9])




```python
np.arange(0, 12, 3)
```




    array([0, 3, 6, 9])




```python
np.arange(0, 6, 0.6)
```




    array([0. , 0.6, 1.2, 1.8, 2.4, 3. , 3.6, 4.2, 4.8, 5.4])




```python
np.arange(0, 12).reshape(3, 4)
```




    array([[ 0,  1,  2,  3],
           [ 4,  5,  6,  7],
           [ 8,  9, 10, 11]])




```python
np.linspace(0,10,5)
```




    array([ 0. ,  2.5,  5. ,  7.5, 10. ])




```python
np.random.random(3)
```




    array([0.71423775, 0.62229946, 0.97150108])




```python
np.random.random((3,3))
```




    array([[0.95155686, 0.10092046, 0.35220847],
           [0.96635154, 0.33764827, 0.44879963],
           [0.40573132, 0.97260092, 0.30105595]])



# 3.4　基本操作　　37
# 3.4.1　算术运算符　　37


```python
a = np.arange(4)
```


```python
a
```




    array([0, 1, 2, 3])




```python
a+4
```




    array([4, 5, 6, 7])




```python
a*2
```




    array([0, 2, 4, 6])




```python
b = np.arange(4,8)
```


```python
b
```




    array([4, 5, 6, 7])




```python
a + b
```




    array([ 4,  6,  8, 10])




```python
a - b
```




    array([-4, -4, -4, -4])




```python
a * b
```




    array([ 0,  5, 12, 21])




```python
a * np.sin(b)
```




    array([-0.        , -0.95892427, -0.558831  ,  1.9709598 ])




```python
a * np.sqrt(b)
```




    array([0.        , 2.23606798, 4.89897949, 7.93725393])




```python
A = np.arange(0, 9).reshape(3, 3)
```


```python
A
```




    array([[0, 1, 2],
           [3, 4, 5],
           [6, 7, 8]])




```python
B = np.ones((3, 3))
```


```python
B
```




    array([[1., 1., 1.],
           [1., 1., 1.],
           [1., 1., 1.]])




```python
A * B
```




    array([[0., 1., 2.],
           [3., 4., 5.],
           [6., 7., 8.]])



# 3.4.2　矩阵积　　38


```python
np.dot(A,B)
```




    array([[ 3.,  3.,  3.],
           [12., 12., 12.],
           [21., 21., 21.]])




```python
A.dot(B)
```




    array([[ 3.,  3.,  3.],
           [12., 12., 12.],
           [21., 21., 21.]])




```python
np.dot(B,A)
```




    array([[ 9., 12., 15.],
           [ 9., 12., 15.],
           [ 9., 12., 15.]])



# 3.4.3　自增和自减运算符　　39


```python
a = np.arange(4)
```


```python
a
```




    array([0, 1, 2, 3])




```python
a += 1
```


```python
a
```




    array([1, 2, 3, 4])




```python
a -= 1
```


```python
a
```




    array([0, 1, 2, 3])




```python
a += 4
```


```python
a
```




    array([4, 5, 6, 7])




```python
a *= 2
```


```python
a
```




    array([ 8, 10, 12, 14])



# 3.4.4　通用函数　　40


```python
a = np.arange(1, 5)
```


```python
a
```




    array([1, 2, 3, 4])




```python
np.sqrt(a)
```




    array([1.        , 1.41421356, 1.73205081, 2.        ])




```python
np.log(a)
```




    array([0.        , 0.69314718, 1.09861229, 1.38629436])




```python
np.sin(a)
```




    array([ 0.84147098,  0.90929743,  0.14112001, -0.7568025 ])



# 3.4.5　聚合函数　　40


```python
a = np.array([3.3, 4.5, 1.2, 5.7, 0.3])
```


```python
a
```




    array([3.3, 4.5, 1.2, 5.7, 0.3])




```python
a.sum()
```




    15.0




```python
a.min()
```




    0.3




```python
a.max()
```




    5.7




```python
a.mean()
```




    3.0




```python
a.std()
```




    2.0079840636817816



# 3.5　索引机制、切片和迭代方法　　41
# 3.5.1　索引机制　　41


```python
a = np.arange(10, 16)
```


```python
a
```




    array([10, 11, 12, 13, 14, 15])




```python
a[4], a[-1], a[-6]
```




    (14, 15, 10)




```python
a[[1, 3, 4]]
```




    array([11, 13, 14])




```python
A = np.arange(10, 19).reshape((3, 3))
```


```python
A
```




    array([[10, 11, 12],
           [13, 14, 15],
           [16, 17, 18]])




```python
A[1, 2]
```




    15



# 3.5.2　切片操作　　42


```python
a = np.arange(10, 16)
```


```python
a
```




    array([10, 11, 12, 13, 14, 15])




```python
a[1:5]
```




    array([11, 12, 13, 14])




```python
a[1:5:2]
```




    array([11, 13])




```python
a[::2]
```




    array([10, 12, 14])




```python
a[:5:2]
```




    array([10, 12, 14])




```python
a[:5:]
```




    array([10, 11, 12, 13, 14])




```python
A = np.arange(10, 19).reshape((3, 3))
```


```python
A
```




    array([[10, 11, 12],
           [13, 14, 15],
           [16, 17, 18]])




```python
 A[0,:]
```




    array([10, 11, 12])




```python
A[:,0]
```




    array([10, 13, 16])




```python
A[0:2, 0:2]
```




    array([[10, 11],
           [13, 14]])




```python
A[[0,2], 0:2]
```




    array([[10, 11],
           [16, 17]])



# 3.5.3　数组迭代　　43


```python
for i in a:
    print(i)
```

    10
    11
    12
    13
    14
    15



```python
for row in A:
    print(row)
```

    [10 11 12]
    [13 14 15]
    [16 17 18]



```python
for item in A.flat:
    print(item)
```

    10
    11
    12
    13
    14
    15
    16
    17
    18



```python
np.apply_along_axis(np.mean, axis=0, arr=A)
```




    array([13., 14., 15.])




```python
np.apply_along_axis(np.mean, axis=1, arr=A)
```




    array([11., 14., 17.])




```python
def foo(x):
    return x/2
```


```python
np.apply_along_axis(foo, axis=1, arr=A)
```




    array([[5. , 5.5, 6. ],
           [6.5, 7. , 7.5],
           [8. , 8.5, 9. ]])




```python
np.apply_along_axis(foo, axis=0, arr=A)
```




    array([[5. , 5.5, 6. ],
           [6.5, 7. , 7.5],
           [8. , 8.5, 9. ]])



# 3.6　条件和布尔数组　　45


```python
A = np.random.random((4, 4))
```


```python
A
```




    array([[0.92122811, 0.55810739, 0.16604965, 0.98756188],
           [0.89990698, 0.45441885, 0.85233406, 0.32187075],
           [0.58032546, 0.01878004, 0.91573127, 0.63144651],
           [0.52282585, 0.57666091, 0.97865543, 0.54473589]])




```python
A < 0.5
```




    array([[False, False,  True, False],
           [False,  True, False,  True],
           [False,  True, False, False],
           [False, False, False, False]])




```python
A[A < 0.5]
```




    array([0.04869294, 0.07669828, 0.26766679, 0.04023488, 0.10839496,
           0.45508628, 0.48141253])



# 3.7　形状变换　　45


```python
a = np.random.random(12)
```


```python
a
```




    array([0.93015885, 0.01067508, 0.43200938, 0.24035759, 0.01564892,
           0.09507133, 0.70143444, 0.46887118, 0.85551184, 0.61722919,
           0.94688342, 0.92520351])




```python
A = a.reshape(3, 4)
```


```python
A
```




    array([[0.93015885, 0.01067508, 0.43200938, 0.24035759],
           [0.01564892, 0.09507133, 0.70143444, 0.46887118],
           [0.85551184, 0.61722919, 0.94688342, 0.92520351]])




```python
a.shape = (3, 4)
```


```python
a
```




    array([[0.93015885, 0.01067508, 0.43200938, 0.24035759],
           [0.01564892, 0.09507133, 0.70143444, 0.46887118],
           [0.85551184, 0.61722919, 0.94688342, 0.92520351]])




```python
a.ravel()
```




    array([0.93015885, 0.01067508, 0.43200938, 0.24035759, 0.01564892,
           0.09507133, 0.70143444, 0.46887118, 0.85551184, 0.61722919,
           0.94688342, 0.92520351])




```python
a.shape = (12)
```


```python
a
```




    array([0.93015885, 0.01067508, 0.43200938, 0.24035759, 0.01564892,
           0.09507133, 0.70143444, 0.46887118, 0.85551184, 0.61722919,
           0.94688342, 0.92520351])




```python
A.transpose()
```




    array([[0.93015885, 0.01564892, 0.85551184],
           [0.01067508, 0.09507133, 0.61722919],
           [0.43200938, 0.70143444, 0.94688342],
           [0.24035759, 0.46887118, 0.92520351]])



# 3.8　数组操作　　46
# 3.8.1　连接数组　　46


```python
A = np.ones((3, 3))
B = np.zeros((3, 3))
```


```python
np.vstack((A, B))
```




    array([[1., 1., 1.],
           [1., 1., 1.],
           [1., 1., 1.],
           [0., 0., 0.],
           [0., 0., 0.],
           [0., 0., 0.]])




```python
np.hstack((A,B))
```




    array([[1., 1., 1., 0., 0., 0.],
           [1., 1., 1., 0., 0., 0.],
           [1., 1., 1., 0., 0., 0.]])




```python
a = np.array([0, 1, 2])
b = np.array([3, 4, 5])
c = np.array([6, 7, 8])
```


```python
np.column_stack((a, b, c))
```




    array([[0, 3, 6],
           [1, 4, 7],
           [2, 5, 8]])




```python
np.row_stack((a, b, c))
```




    array([[0, 1, 2],
           [3, 4, 5],
           [6, 7, 8]])



# 3.8.2　数组切分　　47


```python
A = np.arange(16).reshape((4, 4))
```


```python
A
```




    array([[ 0,  1,  2,  3],
           [ 4,  5,  6,  7],
           [ 8,  9, 10, 11],
           [12, 13, 14, 15]])




```python
[B,C] = np.hsplit(A, 2)
```


```python
B
```




    array([[ 0,  1],
           [ 4,  5],
           [ 8,  9],
           [12, 13]])




```python
C
```




    array([[ 2,  3],
           [ 6,  7],
           [10, 11],
           [14, 15]])




```python
[B,C] = np.vsplit(A, 2)
```


```python
B
```




    array([[0, 1, 2, 3],
           [4, 5, 6, 7]])




```python
C
```




    array([[ 8,  9, 10, 11],
           [12, 13, 14, 15]])




```python
[A1,A2,A3] = np.split(A,[1,3],axis=1)
```


```python
A1
```




    array([[ 0],
           [ 4],
           [ 8],
           [12]])




```python
A2
```




    array([[ 1,  2],
           [ 5,  6],
           [ 9, 10],
           [13, 14]])




```python
A3
```




    array([[ 3],
           [ 7],
           [11],
           [15]])




```python
[A1,A2,A3] = np.split(A,[1,3],axis=0)
```


```python
A1
```




    array([[0, 1, 2, 3]])




```python
A2
```




    array([[ 4,  5,  6,  7],
           [ 8,  9, 10, 11]])




```python
A3
```




    array([[12, 13, 14, 15]])



# 3.9　常用概念　　49
# 3.9.1　对象的副本或视图　　49


```python
a = np.array([1, 2, 3, 4])
b = a
```


```python
b
```




    array([1, 2, 3, 4])




```python
a[2] = 0
```


```python
b
```




    array([1, 2, 0, 4])




```python
c = a[0:2]
```


```python
c
```




    array([1, 2])




```python
a[0] = 0
```


```python
c
```




    array([0, 2])




```python
a = np.array([1, 2, 3, 4])
```


```python
c = a.copy()
```


```python
c
```




    array([1, 2, 3, 4])




```python
a[0] = 0
```


```python
c
```




    array([1, 2, 3, 4])



# 3.9.2　向量化　　50

# 3.9.3　广播机制　　50


```python
A = np.arange(16).reshape(4, 4)
```


```python
b = np.arange(4)
```


```python
A
```




    array([[ 0,  1,  2,  3],
           [ 4,  5,  6,  7],
           [ 8,  9, 10, 11],
           [12, 13, 14, 15]])




```python
b
```




    array([0, 1, 2, 3])




```python
A + b
```




    array([[ 0,  2,  4,  6],
           [ 4,  6,  8, 10],
           [ 8, 10, 12, 14],
           [12, 14, 16, 18]])




```python
m = np.arange(6).reshape(3, 1, 2)
```


```python
n = np.arange(6).reshape(3, 2, 1)
```


```python
m
```




    array([[[0, 1]],
    
           [[2, 3]],
    
           [[4, 5]]])




```python
n
```




    array([[[0],
            [1]],
    
           [[2],
            [3]],
    
           [[4],
            [5]]])




```python
m + n
```




    array([[[ 0,  1],
            [ 1,  2]],
    
           [[ 4,  5],
            [ 5,  6]],
    
           [[ 8,  9],
            [ 9, 10]]])



# 3.10　结构化数组　　52


```python
structured = np.array([(1, 'First', 0.5, 1+2j),(2, 'Second', 1.3, 2-2j), (3, 'Third', 0.8, 1+3j)],dtype=('i2, a6, f4, c8'))
```


```python
structured
```




    array([(1, b'First', 0.5, 1.+2.j), (2, b'Second', 1.3, 2.-2.j),
           (3, b'Third', 0.8, 1.+3.j)],
          dtype=[('f0', '<i2'), ('f1', 'S6'), ('f2', '<f4'), ('f3', '<c8')])




```python
structured = np.array([(1, 'First', 0.5, 1+2j),(2, 'Second', 1.3,2-2j),(3, 'Third', 0.8, 1+3j)],dtype=('int16, a6, float32, complex64'))
```


```python
structured
```




    array([(1, b'First', 0.5, 1.+2.j), (2, b'Second', 1.3, 2.-2.j),
           (3, b'Third', 0.8, 1.+3.j)],
          dtype=[('f0', '<i2'), ('f1', 'S6'), ('f2', '<f4'), ('f3', '<c8')])




```python
structured[1]
```




    (2, b'Second', 1.3, 2.-2.j)




```python
structured['f1']
```




    array([b'First', b'Second', b'Third'], dtype='|S6')




```python
structured = np.array([(1,'First',0.5,1+2j),(2,'Second',1.3,2-2j),(3,'Third',0.8,1+3j)],dtype=[('id','i2'),('position','a6'),('value','f4'),('complex','c8')])
```


```python
structured
```




    array([(1, b'First', 0.5, 1.+2.j), (2, b'Second', 1.3, 2.-2.j),
           (3, b'Third', 0.8, 1.+3.j)],
          dtype=[('id', '<i2'), ('position', 'S6'), ('value', '<f4'), ('complex', '<c8')])




```python
structured.dtype.names = ('id','order','value','complex')
```


```python
structured['order']
```




    array([b'First', b'Second', b'Third'], dtype='|S6')



# 3.11　数组数据文件的读写　　53

# 3.11.1　二进制文件的读写　　54


```python
data = np.random.random(16).reshape(4,4)
```


```python
data
```




    array([[0.87899377, 0.11342252, 0.6682891 , 0.90832539],
           [0.33643193, 0.49913359, 0.46395367, 0.38503354],
           [0.30343769, 0.74571053, 0.16758969, 0.11507577],
           [0.00213888, 0.12661618, 0.71188585, 0.95622255]])




```python
np.save('saved_data',data)
```


```python
loaded_data = np.load('saved_data.npy')
```


```python
loaded_data
```




    array([[0.87899377, 0.11342252, 0.6682891 , 0.90832539],
           [0.33643193, 0.49913359, 0.46395367, 0.38503354],
           [0.30343769, 0.74571053, 0.16758969, 0.11507577],
           [0.00213888, 0.12661618, 0.71188585, 0.95622255]])



# 3.11.2　读取文件中的列表形式数据　　54


```python
data = np.genfromtxt('data.csv', delimiter=',', names=True)
```


```python
data
```




    array([(1., 123., 1.4, 23.), (2., 110., 0.5, 18.), (3., 164., 2.1, 19.)],
          dtype=[('id', '<f8'), ('value1', '<f8'), ('value2', '<f8'), ('value3', '<f8')])




```python
data2 = np.genfromtxt('data2.csv', delimiter=',', names=True)
```


```python
data2
```




    array([(1., 123., 1.4, 23.), (2., 110., nan, 18.), (3.,  nan, 2.1, 19.)],
          dtype=[('id', '<f8'), ('value1', '<f8'), ('value2', '<f8'), ('value3', '<f8')])



# 3.12　小结　　55
