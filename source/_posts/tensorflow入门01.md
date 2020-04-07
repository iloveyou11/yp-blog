---
title: tensorflow入门-1
date: 2020-04-03
categories: AI
author: yangpei
tags:
  - 深度学习
  - tensorflow
comments: true
cover_picture: /images/banner.jpg
---

核心内容：以下介绍tensorflow的基础概念和使用(以tensorflow v1.3为例)

<!-- more -->

### 一、tensorflow基础
#### 基础语法
计算单元有常量`c = tf.constant(2)`，变量`v = tf.Variable(2)`，占位符`p = tf.placeholder(tf.float32)`。常见四则运算：
```python
add = tf.add(a, b)
sub = tf.subtract(a, b)
mul = tf.multiply(a, b)
div = tf.divide(a, b)
```
**session**
有两种方式：
1. sess = tf.Session()直接创建sess，但记得要sess.close()，避免内存泄漏
2. 使用with tf.Session() as sess，利用with关闭会话，意外关闭也会释放资源

注意事项：打印值一定要用sess.run()，否则无法正确打印值
```python
import tensorflow as tf

const1 = tf.constant([[2, 3], [1, 1]])
const2 = tf.constant([[4, 5], [3, 4]])

# matmul 矩阵乘法
mul = tf.matmul(const1, const2)

if const1.graph is tf.compat.v1.get_default_graph():
    print('const1所在的图是当前上下文默认的图')

# 第一种方法 sess.close()关闭会话
sess = tf.Session()
ret = sess.run(mul)
print(ret)
sess.close()

# 第二种方法 利用with关闭会话，意外关闭也会释放资源
# with tf.Session() as sess:
#     ret2 = sess.run(mul)
#     print(ret2)
```

**下面总结下计算过程：**
- 创建数据：可以创建常量、变量和占位符。
- 构建图：通过前面的数据构建一张图。
- 初始化：把变量初始化。
- 计算：必须通过开启一个 Session 来计算图

#### tensorboard

<img width="60%" src="https://i.loli.net/2020/04/03/LMWRuPwGUpKTx8B.jpg" alt="tensorboard" />

```python
import tensorflow as tf

w = tf.compat.v1.Variable(2.0, dtype=tf.float32, name="weight")
b = tf.compat.v1.Variable(1.0, dtype=tf.float32, name="bias")
x = tf.placeholder(dtype=tf.float32, name="input")

with tf.name_scope('output'):
    y = w*x+b

path = './log'

# 创建用于初始化变量的操作
init = tf.global_variables_initializer()

with tf.Session() as sess:
    sess.run(init)
    writer = tf.compat.v1.summary.FileWriter(path, sess.graph)
    result = sess.run(y, {x: 3.0})
    print(result)
```
命令：` tensorboard --logdir=log`,访问http://localhost:6006/即可。

### 二、tensorflow举例
#### 变量、常量
**constant、Variable、placeholder、Parse**
hello world
```python
import tensorflow as tf

hw = tf.constant('hello world!')

sess = tf.Session()
print(sess.run(hw))

sess.close()
```
变量
```python
import tensorflow as tf

state = tf.Variable(0, name='counter')
one = tf.constant(1)

value = tf.add(state, one)
update = tf.assign(state, value)

init = tf.compat.v1.global_variables_initializer()
with tf.Session() as sess:
    sess.run(init)
    for _ in range(3):
        sess.run(update)
        print(sess.run(state))
```
placeholder
```python
import tensorflow as tf

input1 = tf.placeholder(tf.float32)
input2 = tf.placeholder(tf.float32)

output = tf.multiply(input1, input2)

with tf.Session() as sess:
    print(sess.run(output, feed_dict={input1: [7.], input2: [2.]}))
```
#### 线性回归
```python
import tensorflow as tf
import numpy as np

x_data = np.random.rand(100).astype(np.float32)
y_data = x_data*0.1+0.3

Weigths = tf.Variable(tf.random.uniform([1], -1, 1))
biases = tf.Variable(tf.zeros([1]))

y = Weigths*x_data+biases

loss = tf.reduce_mean(tf.square(y-y_data))
optimizer = tf.compat.v1.train.GradientDescentOptimizer(0.5)  # 学习率
train = optimizer.minimize(loss)

init = tf.compat.v1.global_variables_initializer()

# 创建会话，激活init
sess = tf.compat.v1.Session()
sess.run(init)


for step in range(200):
    sess.run(train)
    if step % 20 == 0:
        print(step, sess.run(Weigths), sess.run(biases))

# result：

# 0 [0.85750043] [-0.13565287]
# 20 [0.319864] [0.1856415]
# 40 [0.1624105] [0.26753825]
# 60 [0.11771582] [0.29078543]
# 80 [0.1050288] [0.29738435]
# 100 [0.10142749] [0.29925755]
# 120 [0.10040522] [0.29978925]
# 140 [0.10011502] [0.2999402]
# 160 [0.10003265] [0.29998302]
# 180 [0.10000926] [0.29999518]
```
激活函数模拟
```python
import matplotlib.pyplot as plt
import numpy as np
import tensorflow as tf

x = np.linspace(-10, 10, 200)

# 常见激活函数
def sigmoid(inputs):
    y = [1/float(1+np.exp(-x)) for x in inputs]
    return y
def relu(inputs):
    y = [x*(x > 0) for x in inputs]
    return y
def tanh(inputs):
    y = [(np.exp(x)-np.exp(-x))/float(np.exp(x)+np.exp(-x)) for x in inputs]
    return y
def softplus(inputs):
    y = [np.log(1+np.exp(x)) for x in inputs]

# 经过激活函数处理后的y
y_sigmoid = tf.nn.sigmoid(x)
y_relu = tf.nn.relu(x)
y_tanh = tf.nn.tanh(x)
y_softplus = tf.nn.softplus(x)

sess = tf.Session()
y_sigmoid, y_relu, y_tanh, y_softplus = sess.run(
    [y_sigmoid, y_relu, y_tanh, y_softplus])

# 创建图像
plt.subplot(221)
plt.plot(x, y_sigmoid, c='red', label='sigmoid')
plt.ylim(-0.2, 1.2)
plt.legend(loc='best')

plt.subplot(222)
plt.plot(x, y_relu, c='red', label='relu')
plt.ylim(-1, 6)
plt.legend(loc='best')

plt.subplot(223)
plt.plot(x, y_tanh, c='red', label='tanh')
plt.ylim(-1.3, 1.3)
plt.legend(loc='best')

plt.subplot(224)
plt.plot(x, y_softplus, c='red', label='softplus')
plt.ylim(-1, 6)
plt.legend(loc='best')
plt.show()
sess.close()

```
#### 梯度下降
```python
import matplotlib.pyplot as plt
import numpy as np
import tensorflow as tf

# 构造数据
num = 100
vectors = []

# 正态随机分布函数生成100个点
for i in range(num):
    x1 = np.random.normal(0, 0.6)
    y1 = 0.1*x1+0.2+np.random.normal(0, 0.04)
    vectors.append([x1, y1])

x_data = [v[0] for v in vectors]
y_data = [v[1] for v in vectors]

# 展示所有随机点
# plt.plot(x_data, y_data, 'r*')
# plt.title = 'linear regression using GD'
# plt.legend()
# plt.show()


# 构建线性回归模型
w = tf.Variable(tf.random_uniform([1], -1, 1))
b = tf.Variable(tf.zeros([1]))
y = w*x_data+b

# 损失函数
loss = tf.reduce_mean(tf.square(y-y_data))
# 梯度下降优化器来优化loss function
optimizer = tf.train.GradientDescentOptimizer(0.5)  # 学习率0.5
train = optimizer.minimize(loss)

# 创建会话，并初始化变量
sess = tf.Session()
init = tf.compat.v1.global_variables_initializer()
sess.run(init)

# 训练20步
for step in range(20):
    sess.run(train)
    # 打印每一步损失，w、b
    print("step:%d,loss:%f,weight:%f,bias:%f" %
          (step, sess.run(loss), sess.run(w), sess.run(b)))


# 绘制最佳拟合曲线
plt.plot(x_data, y_data, 'r*', label='original line')
plt.title = 'linear regression using GD'
plt.plot(x_data, sess.run(w)*x_data+sess.run(b), label='fitted line')
plt.legend()
plt.xlabel('x')
plt.ylabel('y')
plt.show()

sess.close()
```
#### 聚类
```python
import matplotlib.pyplot as plt
import numpy as np
import tensorflow as tf
from tensorflow.examples.tutorials.mnist import input_data
mnist = input_data.read_data_sets('mnist_data', one_hot=True)


def add_layer(inputs, in_size, out_size, activation_function=None):
    Weights = tf.Variable(tf.random.normal([in_size, out_size]), name='W')
    baises = tf.Variable(tf.zeros([1, out_size])+0.1, name='b')
    Wx_plus_b = tf.matmul(inputs, Weights)+baises
    if activation_function is None:
        outputs = Wx_plus_b
    else:
        outputs = activation_function(Wx_plus_b)
    return outputs


def compute_accuracy(v_xs, v_ys):
    global prediction
    y_pre = sess.run(prediction, feed_dict={xs: v_xs})
    correct_prediction = tf.equal(tf.argmax(y_pre, 1), tf.argmax(v_ys, 1))
    accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
    result = sess.run(accuracy, feed_dict={xs: v_xs, ys: v_ys})
    return result


# 输入
xs = tf.compat.v1.placeholder(tf.float32, [None, 28*28], name='x_input')
ys = tf.compat.v1.placeholder(tf.float32, [None, 10], name='y_input')
# 输出
prediction = add_layer(xs, 784, 10, activation_function=tf.nn.softmax)
# 交叉熵损失
cross_entropy = tf.reduce_mean(-tf.reduce_sum(ys *
                                              tf.log(prediction), reduction_indices=[1]))
train_step = tf.train.GradientDescentOptimizer(0.5).minimize(cross_entropy)

# 会话
init = tf.compat.v1.global_variables_initializer()
sess = tf.compat.v1.Session()
sess.run(init)

for i in range(1000):
    # 100个100个学习
    batch_xs, batch_ys = mnist.train.next_batch(100)
    sess.run(train_step, feed_dict={
        xs: batch_xs,
        ys: batch_ys
    })
    if i % 50 == 0:
        print(compute_accuracy(mnist.test.images, mnist.test.labels))
```

#### 卷积神经网络CNN
**示例1： 输入1个神经元，隐藏层10个神经元，输出1个神经元，并且动态模拟曲线生成过程**
```python
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt


def add_layer(inputs, in_size, out_size, activation_function=None):
    Weights = tf.Variable(tf.random.normal([in_size, out_size]))
    baises = tf.Variable(tf.zeros([1, out_size])+0.1)
    Wx_plus_b = tf.matmul(inputs, Weights)+baises
    if activation_function is None:
        outputs = Wx_plus_b
    else:
        outputs = activation_function(Wx_plus_b)
    return outputs


# 构造数据
x_data = np.linspace(-1, 1, 300)[:, np.newaxis]
noise = np.random.normal(0, 0.05, x_data.shape)
y_data = np.square(x_data)-0.5+noise

xs = tf.compat.v1.placeholder(tf.float32, [None, 1])
ys = tf.compat.v1.placeholder(tf.float32, [None, 1])

# 构建神经网络
layer1 = add_layer(xs, 1, 10, activation_function=tf.nn.relu)
prediction = add_layer(layer1, 10, 1, activation_function=None)

# 损失函数
loss = tf.reduce_mean(tf.reduce_sum(
    tf.square(ys-prediction), reduction_indices=[1]))
train = tf.train.GradientDescentOptimizer(0.1).minimize(loss)


init = tf.compat.v1.global_variables_initializer()
sess = tf.compat.v1.Session()
sess.run(init)

# 原始数据散点分布图
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
ax.scatter(x_data, y_data)
plt.ion()  # 该程序plt.show()后不暂停，继续往下走
plt.show()

for i in range(1000):
    sess.run(train, feed_dict={xs: x_data, ys: y_data})
    if i % 20 == 0:
        # print(sess.run(loss, feed_dict={xs: x_data, ys: y_data}))

        # 先抹除这条线
        try:
            ax.lines.remove(lines[0])
        except Exception:
            pass

        prediction_value = sess.run(prediction, feed_dict={
                                    xs: x_data, ys: y_data})
        lines = ax.plot(x_data, prediction_value, 'r-', lw=5)
        plt.pause(0.1)
```
**示例2： 整个流程： 卷积——池化——卷积——池化——全连接——全连接**
```python
# 卷积神经网络
import matplotlib.pyplot as plt
import numpy as np
import tensorflow as tf
from tensorflow.examples.tutorials.mnist import input_data
mnist = input_data.read_data_sets('mnist_data', one_hot=True)

def add_layer(inputs, in_size, out_size, activation_function=None):
    Weights = tf.Variable(tf.random.normal([in_size, out_size]), name='W')
    baises = tf.Variable(tf.zeros([1, out_size])+0.1, name='b')
    Wx_plus_b = tf.matmul(inputs, Weights)+baises
    if activation_function is None:
        outputs = Wx_plus_b
    else:
        outputs = activation_function(Wx_plus_b)
    return outputs


def compute_accuracy(v_xs, v_ys):
    global prediction
    y_pre = sess.run(prediction, feed_dict={xs: v_xs})
    correct_prediction = tf.equal(tf.argmax(y_pre, 1), tf.argmax(v_ys, 1))
    accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
    result = sess.run(accuracy, feed_dict={xs: v_xs, ys: v_ys})
    return result


def weight_variables(shape):
    initial = tf.truncated_normal(shape, stddev=0.1)
    return tf.Variable(initial)


def bias_variables(shape):
    initial = tf.constant(0.1, shape=shape)
    return tf.Variable(initial)

# 卷积层
def conv2d(x, W):
    # strides格式 [1,x_movement,y_movement,1]
    return tf.nn.conv2d(x, W, strides=[1, 1, 1, 1], padding='SAME')

# 池化层
def max_pool_2x2(x):
    return tf.nn.max_pool(x, ksize=[1, 2, 2, 1], strides=[1, 2, 2, 1], padding='SAME')


# 输入
xs = tf.compat.v1.placeholder(tf.float32, [None, 28*28], name='x_input')
ys = tf.compat.v1.placeholder(tf.float32, [None, 10], name='y_input')
keep_prob = tf.compat.v1.placeholder(tf.float32)
x_image = tf.reshape(xs, [-1, 28, 28, 1])


# 整个流程：
# 卷积——池化——卷积——池化——全连接——全连接

# conv1 layer
W_conv1 = weight_variables([5, 5, 1, 32])  # 5*5 in_size=1 out_size=32
b_conv1 = bias_variables([32])
h_conv1 = tf.nn.relu(conv2d(x_image, W_conv1)+b_conv1)  # output 28*28*32
h_pool1 = max_pool_2x2(h_conv1)  # output 14*14*32

# conv2 layer
W_conv2 = weight_variables([5, 5, 32, 64])  # 5*5 in_size=32 out_size=64
b_conv2 = bias_variables([32])
h_conv2 = tf.nn.relu(conv2d(h_pool1, W_conv2)+b_conv2)  # output 14*14*64
h_pool2 = max_pool_2x2(h_conv2)  # output 7*7*64

# func1 layer
W_f1 = weight_variables([7*7*64, 1024])
b_f1 = bias_variables([1024])
h_pool2_flat = tf.reshape(h_pool2, [-1, 7*7*64])
h_f1 = tf.nn.relu(tf.matmul(h_pool2_flat, W_f1)+b_f1)
h_f1_drop = tf.nn.dropout(h_f1, keep_prob)

# func2 layer
W_f2 = weight_variables([1024, 10])
b_f2 = bias_variables([10])
prediction = tf.nn.softmax(tf.matmul(h_f1_drop, W_f2)+b_f2)


# 输出
prediction = add_layer(xs, 784, 10, activation_function=tf.nn.softmax)
# 交叉熵损失
cross_entropy = tf.reduce_mean(-tf.reduce_sum(ys *
                                              tf.log(prediction), reduction_indices=[1]))
train_step = tf.train.AdamOptimizer(1e-4).minimize(cross_entropy)

# 会话
init = tf.compat.v1.global_variables_initializer()
sess = tf.compat.v1.Session()
sess.run(init)

for i in range(1000):
    # 100个100个学习
    batch_xs, batch_ys = mnist.train.next_batch(100)
    sess.run(train_step, feed_dict={
        xs: batch_xs,
        ys: batch_ys
    })
    if i % 50 == 0:
        print(compute_accuracy(mnist.test.images, mnist.test.labels))

```
**示例3： 完整的CNN示例，包括输出层、池化层、卷积层、dropout层、全连接层等**

<img width="60%" src="https://i.loli.net/2020/04/03/SKXhFBveYbE24wx.jpg" alt="CNN示例3" />

```python
import matplotlib.pyplot as plt
import numpy as np
import tensorflow as tf

# 载入手写数字库(55000*28*28)
from tensorflow.examples.tutorials.mnist import input_data

# one_hot 独热吗的编码形式
# 0：100000000
# 1：010000000
# 2：001000000
# ……
mnist = input_data.read_data_sets('mnist_data', one_hot=True)


# None表示张量的第一个维度
input_x = tf.placeholder(tf.float32, [None, 28*28])/255
output_y = tf.placeholder(tf.float32, [None, 10])
input_x_images = tf.reshape(input_x, [-1, 28, 28, 1])

# 从测试数据集中选取3000个图片和标签
test_x = mnist.test.images[:3000]
test_y = mnist.test.labels[:3000]

# 构建卷积神经网络

# 第一层卷积，输入28*28*1，卷积5*5*32，输出28*28*32
conv1 = tf.keras.layers.Conv2D(
    inputs=input_x_images,  # 输入
    filters=32,  # 过滤器，输出深度
    kernel_size=5,  # 卷积大小
    strides=1,  # 步长
    padding='same',  # padding,same表示输出大小不变，因此需要补两圈,
    activation=tf.nn.relu
)

# 第一层池化（亚采样），输入28*28*32，输出14*14*32
pool1 = tf.keras.layers.MaxPooling2D(
    inputs=conv1,  # 输入
    pool_size=[2, 2],  # 过滤器大小
    strides=2,  # 步长
)

# 第二层卷积，输入14*14*32，卷积5*5*64，输出14*14*64
conv2 = tf.keras.layers.Conv2D(
    inputs=pool1,  # 输入
    filters=64,  # 过滤器，输出深度
    kernel_size=5,  # 卷积大小
    strides=1,  # 步长
    padding='same',  # padding,same表示输出大小不变，因此需要补两圈,
    activation=tf.nn.relu
)

# 第二层池化，输入14*14*64，输出7*7*64
pool2 = tf.keras.layers.MaxPooling2D(
    inputs=conv2,  # 输入
    pool_size=[2, 2],  # 过滤器大小
    strides=2,  # 步长
)

# 平坦化
flat = tf.reshape(pool2, [-1, 7*7*64])
# 全连接层
dense = tf.keras.layers.Dense(
    inputs=flat,
    units=1024,
    activation=tf.nn.relu
)
# dropouts
dropout = tf.keras.layers.Dropout(inputs=dense, rate=0.5, training=True)
# 10个神经元全连接层，不用激活函数做非线性化，输出1*1*10
logits = tf.keras.layers.Dense(inputs=dropout, units=10)


# 计算误差(计算交叉熵，再用softmax计算概率)
loss = tf.losses.softmax_cross_entropy(onehot_labels=output_y, logits=logits)
# 用Adam优化器最小化误差，学习率0.001
train_op = tf.compat.v1.train.AdamOptimizer(learning_rate=0.001).minimize(loss)
# 精度，计算预测值和实际标签的匹配程度
accuracy = tf.compat.v1.metrics.accuracy(
    labels=tf.argmax(output_y, axis=1),
    predictions=tf.argmax(logits, axis=1)[1]
)


# 创建会话
sess = tf.compat.v1.Session()
# 初始化变量：全局和局部
init = tf.group(tf.compat.v1.global_variables_initializer,
                tf.compat.v1.local_variables_initializer)
sess.run(init)

for i in range(20000):
  # 从train训练集中取下一50个样本
    batch = mnist.train.next_batch(50)
    train_loss, train_op = sess.run([loss, train_op], {
        input_x: batch[0],
        output_y: batch[1]}
    )
    if i % 100 == 0:
        test_accuracy = sess.run(accuracy, {
            input_x: test_x,
            output_y: test_y
        })
        print('step:%d,loss=%.4f,test_accuracy:%.2f' %
              (i, train_loss, test_accuracy))


# 测试：打印20个预测值和真实值比对
test_output = sess.run(logits, {input_x: test_x[:20]})
predict_y = np.argmax(test_output, 1)
print(predict_y, 'predict_y')
print(np.argmax(test_y[:20], 1), 'real number')
```

#### 保存与读取
tensorflow目前只能保存Variable，不能保存整个神经框架，需要重新定义一下框架，再把Variable放进来重新学习 

<img width="60%" src="https://i.loli.net/2020/04/03/RJitKcPol4dhOnI.jpg" alt="CNN示例3" />

**存入文件：**
```python
import tensorflow as tf
import numpy as np

# save to file
# remember to define the same dtype and shape where restore
W = tf.Variable([[1, 2, 3], [4, 5, 6]], dtype=tf.float32)
b = tf.Variable([[1, 2, 3]], dtype=tf.float32)

init = tf.compat.v1.global_variables_initializer()

saver = tf.compat.v1.train.Saver()

with tf.compat.v1.Session() as sess:
    sess.run(init)
    # 这里tensorflow推荐使用ckpt后缀名
    saver_path = saver.save(sess, 'net/save_net.ckpt')
```
**读取文件：**
```python
import tensorflow as tf
import numpy as np

# restore variables
# redefine same dtype and shape for your variables
# define empty framework

W = tf.Variable(np.arange(6).reshape((2, 3)), dtype=tf.float32)
b = tf.Variable(np.arange(3).reshape((1, 3)), dtype=tf.float32)

# not need init step

saver = tf.compat.v1.train.Saver()
with tf.compat.v1.Session() as sess:
    saver.restore(sess, 'net/save_net.ckpt')
    print('weight:', sess.run(W))
    print('bias:', sess.run(b))
```

### 三、tensorflow实战
必须掌握的前置知识：
- 线性回归
- 逻辑回归
- 决策树
- 随机森林
- 最近邻算法（KNN）
- 朴素贝叶斯
- 支持向量机（SVM）
- 感知机
- 深度神经网络
#### 房价预测模型（线性回归）
掌握要点：
- 数据预处理（特征归一化）
- tensorflow训练模型的工作流
- 数据可视化库matplotlib & seaborn & mplot3d
#### 手写数字识别
#### 验证码识别
#### 人脸识别