---
title: TensorFlow实战-第3章-TensorFlow第一步
date: 2018-04-17 16:56:31
tags:
 - 机器学习
 - TensorFlow
categories:
 - 机器学习
 - TensorFlow
---


# 3 TensorFlow第一步 39
## 3.1 TensorFlow的编译及安装 39

- install anconda
[link](TensorFlow实战-第3章-TensorFlow第一步/https://www.anaconda.com/download/)

- CPU version
conda install tensorflow

- GPU version
conda install tensorflow-gpu

## 3.2 TensorFlow实现SoftmaxRegression识别手写数字 46

### 导入数据
MNIST是一个非常简单的机器视觉数据集，由几万张28x28像素的手写数字组成，这些图片只包含灰度信息。

```python
from tensorflow.examples.tutorials.mnist import input_data
mnist = input_data.read_data_sets("MNIST_data/", one_hot=True)

print(mnist.train.images.shape, mnist.train.labels.shape)
print(mnist.test.images.shape, mnist.test.labels.shape)
print(mnist.validation.images.shape, mnist.validation.labels.shape)
```

    Extracting MNIST_data/train-images-idx3-ubyte.gz
    Extracting MNIST_data/train-labels-idx1-ubyte.gz
    Extracting MNIST_data/t10k-images-idx3-ubyte.gz
    Extracting MNIST_data/t10k-labels-idx1-ubyte.gz
    (55000, 784) (55000, 10)
    (10000, 784) (10000, 10)
    (5000, 784) (5000, 10)

可以看到数据集中，训练集有55000个样本，测试集有10000个样本，验证集有5000个样本。
这里的数据全部丢弃了图片的二维结构信息，只是把一张图片变成了一个很长的一维向量。
我们可以使用matplotlib把一维数据还原为原来的图像。如下所示。

```python
from matplotlib import pyplot as plt
% matplotlib inline

data = mnist.train.images[0].reshape((28,28))
plt.imshow(data)

print(mnist.train.labels[0])
```

    [0. 0. 0. 0. 0. 0. 0. 1. 0. 0.]

![png](TensorFlow实战-第3章-TensorFlow第一步/output_5_1.png)

训练数据(55000, 784)，第一维是图片的编号，第二维是图片中像素点的编号。
对10个种类的标签进行了onehot编码，如上所示，[0. 0. 0. 0. 0. 0. 0. 1. 0. 0.] 代表为7

![Snipaste_2018-04-17_17-33-06.png](TensorFlow实战-第3章-TensorFlow第一步/Snipaste_2018-04-17_17-33-06.png)

### 准备好数据以后，接下来就要设计算法了
这里使用了一个叫做softmax regression的算法来训练分类模型。

当我们处理分类任务时，通常需要使用softmax regression模型。即使后面章节中的CNN或者RNN，如果是分类模型，最后一层同样也是softmax regression。
它的工作原理很简单，将可以判定为某类的特征想加，然后将这些特征转化为判定为这一类的概率。
这些特征可以通过一些简单的方法得到，比如对所有像素求一个加权和，而权重是根据数据自动学习、训练出来的。比如某一个像素的灰度值大代表可能是数字n的概率越大。

如下公式：
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
$$feature_i = \sum_j W_i_jx_j + b_i$$
其中$i$为第$i$类，$j$代表一张图片的第$j$个像素。$b_i$是bias。

接下来计算所有特征的softmax，简单的说就是计算一个exp函数，然后再进行标准化（让所有类别的输出的概率值和为1）。
$$softmax(x) = normalize(exp(x))$$
其中判定为第i类的概率是：
$$softmax(x)\_i = \frac {exp(x)}{\sum_j exp(x_j)}$$

Snipaste_2018-04-17_18-49-31.png
Snipaste_2018-04-17_18-49-40.png
Snipaste_2018-04-17_18-49-50.png

可以用下面一行简洁的表达：
$$y = softmax(Wx + b)$$

```python
import tensorflow as tf

# 创建InteractiveSession，使用这个命令会将这个session注册为默认的session，
# 之后的计算也默认跑在这个session里面。
# 不同session之间的数据和运算都是相互独立的。
sess = tf.InteractiveSession()

# 创建用于输入数据的placeholder，第一个参数是数据类型，第二个参数代表tensor的shape。
# None表示不限制条数，784是表示一个784维的向量。
x = tf.placeholder(tf.float32, [None, 784])

#接下来要为softmax regression模型中的weight和biases创建Variable对象。
#Variable中的数据是持久化的，长期存在并且在每轮迭代中被更新。
#这里把weight和bias全部初始化为0，因为模型训练时会自动学习合适的值，所以对这个简单模型来说初始值不太重要。
#但是对于复杂CNN RNN或者其他比较深的全连接网络来说，初始化的方法比较重要，甚至至关重要。
W = tf.Variable(tf.zeros([784, 10]))
b = tf.Variable(tf.zeros([10]))

# 实现公式y = softmax(Wx + b)
#tf.nn包含了大量的神经网络组件，softmax是其中之一。
#tf.matmul是矩阵乘法函数
y = tf.nn.softmax(tf.matmul(x, W) + b)

# 为了训练模型，需要定义一个loss function来描述模型对问题的分类精度。
#loss越小，代表模型分类结果和真实值的偏差越小，也就是说模型越准确。
y_ = tf.placeholder(tf.float32, [None, 10])
cross_entropy = tf.reduce_mean(-tf.reduce_sum(y_ * tf.log(y), reduction_indices=[1]))
```


```python
train_step = tf.train.GradientDescentOptimizer(0.5).minimize(cross_entropy)
tf.global_variables_initializer().run()
```


```python
for i in range(1000):
    batch_xs, batch_ys = mnist.train.next_batch(100)
    train_step.run({x: batch_xs, y_: batch_ys})
```


```python
correct_prediction = tf.equal(tf.argmax(y, 1), tf.argmax(y_, 1))
accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))

```


```python
print(accuracy.eval({x: mnist.test.images, y_: mnist.test.labels}))
```

    0.9194

