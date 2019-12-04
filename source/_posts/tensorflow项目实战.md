---
title: tensorflow项目实战
date: 2019-12-04
categories: AI
author: yangpei
tags:
  - 深度学习
  - 计算机视觉
comments: true
cover_picture: /images/banner.jpg
---

利用tensorflow框架写一些小项目,多多熟悉一下吧~

<!-- more -->

[github项目地址](https://github.com/iloveyou11/tensorflow-projects)

#### 任务：房价预测线性回归
**第1步:进行数据处理**

首先读取csv数据,再对x1 x2 ... xn y数据进行归一化处理,接下来添加单独的一列x0(值均为1,常数项)
<img alt="回归模型1" src="https://i.loli.net/2019/12/04/pS4qj1zguRPa7lY.jpg" width="50%" />

```python
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
from mpl_toolkits import mplot3d

# 加载数据
sns.set(context="notebook", style="whitegrid", palette="dark")
df0 = pd.read_csv('房价预测线性回归/data0.csv', names=['square', 'price'])
sns.lmplot('square', 'price', df0, height=6, fit_reg=True)

df1 = pd.read_csv('房价预测线性回归/data1.csv', names=['square', 'bedrooms', 'price'])
print(df1.head())

# 绘制3d散点图
fig = plt.figure()
ax = plt.axes(projection='3d')
ax.set_xlabel('square')
ax.set_ylabel('bedrooms')
ax.set_zlabel('price')
ax.scatter3D(df1['square'], df1['bedrooms'],
             df1['price'], c=df1['price'], cmap='Greens')

# 数据规范化
def normalize(df):
    return df.apply(lambda col: (col-col.mean())/col.std())


df = normalize(df1)
print(df.head())

# 绘制规范化数据后的3d散点图
fig = plt.figure()
ax = plt.axes(projection='3d')
ax.set_xlabel('square')
ax.set_ylabel('bedrooms')
ax.set_zlabel('price')
ax.scatter3D(df['square'], df['bedrooms'],
             df['price'], c=df['price'], cmap='Reds')
plt.show()

# 添加列
ones = pd.DataFrame({'ones': np.ones(len(df))})
df = pd.concat([ones, df], axis=1)
print(df.head())
```
<img alt="回归模型2" src="https://i.loli.net/2019/12/04/TSDOhWt62yqJG79.jpg" width="50%" />
<img alt="回归模型3" src="https://i.loli.net/2019/12/04/isW5zX1EYHSpnw7.jpg" width="50%" />

经过归一化处理后的数据结构如下:
```
   ones    square  bedrooms     price
0   1.0  0.130010 -0.223675  0.475747
1   1.0 -0.504190 -0.223675 -0.084074
2   1.0  0.502476 -0.223675  0.228626
3   1.0 -0.735723 -1.537767 -0.867025
4   1.0  1.257476  1.090417  1.595389
```
**第2步:训练模型**

在第1步得到的数据基础上进行处理. 首先,需要拿到x y的数据,定义学习率`learning_rate`和训练次数`epoch`,分别输入x y,计算损失loss值,使用梯度下降优化器进行优化操作.`GradientDescentOptimizer`
```python
import tensorflow as tf
import numpy as np
import pandas as pd

def normalize(df):
    return df.apply(lambda col: (col-col.mean())/col.std())

df = pd.read_csv('房价预测线性回归/data1.csv', names=['square', 'bedrooms', 'price'])
df = normalize(df)
ones = pd.DataFrame({'ones': np.ones(len(df))})
df = pd.concat([ones, df], axis=1)
# print(df.head())


# 数据处理
X_data = np.array(df[df.columns[0:3]])
y_data = np.array(df[df.columns[-1]]).reshape(len(df), 1)
print(X_data.shape, type(X_data))
print(y_data.shape, type(y_data))


# 创建显性回归模型
learning_rate = 0.01
epoch = 500
# 输入x y
X = tf.compat.v1.placeholder(tf.float32, X_data.shape)
y = tf.compat.v1.placeholder(tf.float32, y_data.shape)
W = tf.compat.v1.get_variable(
    "weights", (X_data.shape[1], 1), initializer=tf.constant_initializer())
y_pred = tf.matmul(X, W)
loss_op = 1 / (2 * len(X_data)) * tf.matmul((y_pred - y),
                                            (y_pred - y), transpose_a=True)
opt = tf.train.GradientDescentOptimizer(learning_rate=learning_rate)
train_op = opt.minimize(loss_op)


# 创建会话
with tf.compat.v1.Session() as sess:
    sess.run(tf.compat.v1.global_variables_initializer())
    for e in range(1, epoch+1):
        sess.run(train_op, feed_dict={X: X_data, y: y_data})
        if e % 10 == 0:
            loss, w = sess.run([loss_op, W], feed_dict={X: X_data, y: y_data})
            print("Epoch %d \t Loss=%.4g \t Model: y = %.4gx1 + %.4gx2 + %.4g" %
                  (e, loss, w[1], w[2], w[0]))

```
模型训练好后的数据如下:
```
Epoch 10         Loss=0.4116     Model: y = 0.0791x1 + 0.03948x2 + 3.353e-10
Epoch 20         Loss=0.353      Model: y = 0.1489x1 + 0.07135x2 + -5.588e-11
Epoch 30         Loss=0.3087     Model: y = 0.2107x1 + 0.09676x2 + 3.912e-10
Epoch 40         Loss=0.2748     Model: y = 0.2655x1 + 0.1167x2 + -1.863e-11
Epoch 50         Loss=0.2489     Model: y = 0.3142x1 + 0.1321x2 + 1.77e-10
Epoch 60         Loss=0.2288     Model: y = 0.3576x1 + 0.1436x2 + -4.47e-10
Epoch 70         Loss=0.2131     Model: y = 0.3965x1 + 0.1519x2 + -8.103e-10
Epoch 80         Loss=0.2007     Model: y = 0.4313x1 + 0.1574x2 + -6.985e-10
Epoch 90         Loss=0.1908     Model: y = 0.4626x1 + 0.1607x2 + -4.936e-10
......
......
Epoch 420        Loss=0.1332     Model: y = 0.8076x1 + 0.02271x2 + 2.125e-09
Epoch 430        Loss=0.133      Model: y = 0.8109x1 + 0.01957x2 + 2.292e-09
Epoch 440        Loss=0.1328     Model: y = 0.8141x1 + 0.01655x2 + 2.913e-09
Epoch 450        Loss=0.1326     Model: y = 0.8171x1 + 0.01366x2 + 3.412e-09
Epoch 460        Loss=0.1325     Model: y = 0.82x1 + 0.01087x2 + 3.749e-09
Epoch 470        Loss=0.1323     Model: y = 0.8228x1 + 0.008204x2 + 3.499e-09
Epoch 480        Loss=0.1322     Model: y = 0.8254x1 + 0.005641x2 + 3.663e-09
Epoch 490        Loss=0.1321     Model: y = 0.828x1 + 0.003183x2 + 4.2e-09
Epoch 500        Loss=0.132      Model: y = 0.8304x1 + 0.0008239x2 + 4.138e-09
```
**第3步:可视化流图**

使用tensorboard可以可视化数据流图,可以方便我们查看训练的过程
```python
import tensorflow as tf
import numpy as np
import pandas as pd

def normalize(df):
    return df.apply(lambda col: (col-col.mean())/col.std())

df = pd.read_csv('房价预测线性回归/data1.csv', names=['square', 'bedrooms', 'price'])
df = normalize(df)
ones = pd.DataFrame({'ones': np.ones(len(df))})
df = pd.concat([ones, df], axis=1)
# print(df.head())

# 数据处理
X_data = np.array(df[df.columns[0:3]])
y_data = np.array(df[df.columns[-1]]).reshape(len(df), 1)
print(X_data.shape, type(X_data))
print(y_data.shape, type(y_data))

# 创建显性回归模型
learning_rate = 0.01
epoch = 500
# 输入x y
with tf.name_scope('input'):
    X = tf.compat.v1.placeholder(tf.float32, X_data.shape)
    y = tf.compat.v1.placeholder(tf.float32, y_data.shape)
with tf.name_scope('hypothesis'):
    W = tf.compat.v1.get_variable(
        "weights", (X_data.shape[1], 1), initializer=tf.constant_initializer())
    y_pred = tf.matmul(X, W)
with tf.name_scope('loss'):
    loss_op = 1 / (2 * len(X_data)) * tf.matmul((y_pred - y),
                                                (y_pred - y), transpose_a=True)
with tf.name_scope('train'):
    train_op = tf.train.GradientDescentOptimizer(
        learning_rate=learning_rate).minimize(loss_op)

# 创建会话
with tf.compat.v1.Session() as sess:
    sess.run(tf.compat.v1.global_variables_initializer())
    writer = tf.compat.v1.summary.FileWriter('./summary', sess.graph)
    for e in range(1, epoch+1):
        sess.run(train_op, feed_dict={X: X_data, y: y_data})
        if e % 10 == 0:
            loss, w = sess.run([loss_op, W], feed_dict={X: X_data, y: y_data})
            print("Epoch %d \t Loss=%.4g \t Model: y = %.4gx1 + %.4gx2 + %.4g" %
                  (e, loss, w[1], w[2], w[0]))
writer.close()
```
其中,定义的`with tf.name_scope('xxx'):`是为了将相关的部分视为整体展示在tensorboard中,可以更加方便地展开和隐藏,能够更有效地展示模型的结构.

**第4步:可视化损失loss**

```python
import seaborn as sns
import matplotlib.pyplot as plt
import tensorflow as tf
import numpy as np
import pandas as pd

def normalize(df):
    return df.apply(lambda col: (col-col.mean())/col.std())

df = pd.read_csv('房价预测线性回归/data1.csv', names=['square', 'bedrooms', 'price'])
df = normalize(df)
ones = pd.DataFrame({'ones': np.ones(len(df))})
df = pd.concat([ones, df], axis=1)
# print(df.head())


# 数据处理
X_data = np.array(df[df.columns[0:3]])
y_data = np.array(df[df.columns[-1]]).reshape(len(df), 1)
print(X_data.shape, type(X_data))
print(y_data.shape, type(y_data))

# 创建显性回归模型
learning_rate = 0.01
epoch = 500
# 输入x y
with tf.name_scope('input'):
    X = tf.compat.v1.placeholder(tf.float32, X_data.shape)
    y = tf.compat.v1.placeholder(tf.float32, y_data.shape)
with tf.name_scope('hypothesis'):
    W = tf.compat.v1.get_variable(
        "weights", (X_data.shape[1], 1), initializer=tf.constant_initializer())
    y_pred = tf.matmul(X, W)
with tf.name_scope('loss'):
    loss_op = 1 / (2 * len(X_data)) * tf.matmul((y_pred - y),
                                                (y_pred - y), transpose_a=True)
with tf.name_scope('train'):
    train_op = tf.train.GradientDescentOptimizer(
        learning_rate=learning_rate).minimize(loss_op)

# 创建会话
with tf.compat.v1.Session() as sess:
    sess.run(tf.compat.v1.global_variables_initializer())
    writer = tf.compat.v1.summary.FileWriter('./summary', sess.graph)
    # 记录所有损失值
    loss_data = []
    for e in range(1, epoch+1):
        _, loss, w = sess.run([train_op, loss_op, W],
                              feed_dict={X: X_data, y: y_data})
        loss_data.append(float(loss))
        if e % 100 == 0:
            log_str = "Epoch %d \t Loss=%.4g \t Model: y = %.4gx1 + %.4gx2 + %.4g"
            print(log_str % (e, loss, w[1], w[2], w[0]))
writer.close()
# print(len(loss_data))

# 可视化损失值
sns.set(context="notebook", style="whitegrid", palette="dark")
ax = sns.lineplot(x='epoch', y='loss', data=pd.DataFrame(
    {'loss': loss_data, 'epoch': np.arange(epoch)}))
ax.set_xlabel('epoch')
ax.set_ylabel('loss')
plt.show()
```
每次迭代过程中,损失值的变化趋势如下:
<img alt="回归模型4" src="https://i.loli.net/2019/12/04/Kf2Wnlhwq1uXaQd.jpg" width="50%" />
由此可见,随着迭代次数的增多,损失值越来越小,最后趋于平稳,模型越来越优.


#### 任务：手写数字识别

**第1步:加载数据集mnist**
```python
# 获取mnist数据集
import matplotlib.pyplot as plt
from keras.datasets import mnist

(train_x, train_y), (test_x, test_y) = mnist.load_data()
print(train_x.shape, train_y.shape)
print(test_x.shape, test_y.shape)

# 可视化数据集
fig = plt.figure()
for i in range(15):
    plt.subplot(3, 5, i+1)
    plt.tight_layout()
    plt.imshow(train_x[i], cmap='Greys')
    plt.title('label:{}'.format(train_y[i]))
    plt.xticks([])
    plt.yticks([])
plt.show()
```
最后得到的数据集如下:
<img alt="手写数字1" src="https://i.loli.net/2019/12/04/9WyiPqcbEkVTmrl.jpg" width="50%" />

**第2步:利用softmax进行手写数字识别**

具体流程: 
1. 统计训练数据中各标签数量,并可视化标签数量,保证各类数字数量差不多,这样可以保证接下来训练模型的可靠性
2. 数据处理：one-hot 编码
3. 使用 Keras sequential model 定义神经网络(在这里,简单地使用了`Dense-Activation(relu)-Dense-Activation(relu)-Dense-Activation(softmax)`的结构),通过softmax计算的概率值大小判断是哪个数字的可能性最大
4. 编译模型(利用`model.compile()`进行模型的编译)
5. 训练模型，并将指标保存到 history 中
6. 保存模型(利用`model.save()`将模型保存到本地,下次可以直接使用此模型),官方解释如下:
```
You can use model.save(filepath) to save a Keras model into a single HDF5 file which will contain:

the architecture of the model, allowing to re-create the model
the weights of the model
the training configuration (loss, optimizer)
the state of the optimizer, allowing to resume training exactly where you left off.
You can then use keras.models.load_model(filepath) to reinstantiate your model. load_model will also take care of compiling the model using the saved training configuration (unless the model was never compiled in the first place).
```

```python
import tensorflow as tf
import os
from keras.layers.core import Dense, Activation
from keras.models import Sequential
from keras.utils import np_utils
import matplotlib.pyplot as plt
import numpy as np
from keras.datasets import mnist

# 获取数据集
(x_train, y_train), (x_test, y_test) = mnist.load_data()

# 规范化
X_train = x_train.reshape(60000, 784)
X_test = x_test.reshape(10000, 784)
X_train = X_train.astype('float32')
X_test = X_test.astype('float32')
X_train /= 255
X_test /= 255

# 统计各标签数量
label, count = np.unique(y_train, return_counts=True)
# print(label, count)

# 可视化标签数量
fig = plt.figure()
plt.bar(label, count, width=0.7, align='center')
plt.title("Label Distribution")
plt.xlabel("Label")
plt.ylabel("Count")
plt.xticks(label)
plt.ylim(0, 7500)
for label, count in zip(label, count):
    plt.text(label, count, '%d' % count, ha='center', va='bottom', fontsize=10)
# plt.show()

# one-hot编码
n_classes = 10
# print('before one-hot:', y_train.shape)
Y_train = np_utils.to_categorical(y_train, n_classes)
# print('after one-hot:', Y_train.shape)
Y_test = np_utils.to_categorical(y_test, n_classes)

# 定义神经网络
model = Sequential()

model.add(Dense(512, input_shape=(784,)))
model.add(Activation('relu'))

model.add(Dense(512))
model.add(Activation('relu'))

model.add(Dense(10))
model.add(Activation('softmax'))

# 编译模型
model.compile(loss='categorical_crossentropy',
              metrics=['accuracy'], optimizer='adam')
# 开始训练
history = model.fit(
    X_train,
    Y_train,
    batch_size=128,
    epochs=5,
    verbose=2,
    validation_data=(X_test, Y_test)
)

# 可视化指标
# print(history.history)
fig = plt.figure()

plt.subplot(2, 1, 1)
plt.plot(history.history['accuracy'])
plt.plot(history.history['val_accuracy'])
plt.title('Model Accuracy')
plt.ylabel('accuracy')
plt.xlabel('epoch')
plt.legend(['train', 'test'], loc='lower right')

plt.subplot(2, 1, 2)
plt.plot(history.history['loss'])  # 损失
plt.plot(history.history['val_loss'])  # 测试集上的损失
plt.title('Model Loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train', 'test'], loc='upper right')
plt.tight_layout()
# plt.show()

# 保存模型
save_dir = "./mnist/model/"
if tf.io.gfile.exists(save_dir):
    tf.io.gfile.rmtree(save_dir)
tf.io.gfile.makedirs(save_dir)

model_name = 'keras_mnist.h5'
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)
```

训练的结果为:
```
Train on 60000 samples, validate on 10000 samples
Epoch 1/5
 - 10s - loss: 0.2186 - acc: 0.9353 - val_loss: 0.0935 - val_acc: 0.9709
Epoch 2/5
 - 11s - loss: 0.0788 - acc: 0.9754 - val_loss: 0.0757 - val_acc: 0.9749
Epoch 3/5
 - 11s - loss: 0.0494 - acc: 0.9837 - val_loss: 0.0636 - val_acc: 0.9807
Epoch 4/5
 - 12s - loss: 0.0353 - acc: 0.9888 - val_loss: 0.0762 - val_acc: 0.9769
Epoch 5/5
 - 11s - loss: 0.0265 - acc: 0.9910 - val_loss: 0.0724 - val_acc: 0.9786
```
可视化指标效果如下:
<img alt="手写数字2" src="https://i.loli.net/2019/12/04/2eY6HaTyqbkKZOr.jpg" width="50%" />

保存模型后,如果要实现模型的加载,则可以使用`load_model`函数,其中`model_path`是模型的路径.使用训练好的此模型统计测试集上的分类结果,具体代码如下:
```python
from keras.models import load_model
import os
import numpy as np
from keras.utils import np_utils
from keras.datasets import mnist

(x_train, y_train), (x_test, y_test) = mnist.load_data()
# 规范化
X_test = x_test.reshape(10000, 784)
X_test = X_test.astype('float32')
X_test /= 255

n_classes = 10
Y_test = np_utils.to_categorical(y_test, n_classes)

save_dir = "./mnist/model/"
model_name = 'keras_mnist.h5'
model_path = os.path.join(save_dir, model_name)
mnist_model = load_model(model_path)

loss_and_metrics = mnist_model.evaluate(X_test, Y_test, verbose=2)
print("Test Loss: {}".format(loss_and_metrics[0]))
print("Test Accuracy: {}%".format(loss_and_metrics[1]*100))

predicted_classes = mnist_model.predict_classes(X_test)

correct = np.nonzero(predicted_classes == y_test)[0]
incorrect = np.nonzero(predicted_classes != y_test)[0]
print("Classified correctly count: {}".format(len(correct)))
print("Classified incorrectly count: {}".format(len(incorrect)))
```
得到的结果如下:
```
Test Loss: 0.07241645678399945
Test Accuracy: 97.86%
Classified correctly count: 9786
Classified incorrectly count: 214
```
由此可见,训练结果还是相当不错的,准确率达到了97.86%.

**第3步:利用cnn进行手写数字识别**

整体的流程和第2步是非常类似的,只是在模型训练的过程中,采用的是cnn卷积神经网络,加入了更多的隐藏层,增加了模型的复杂度,也提高了模型的准确性.

具体的神经网络设计如下,分别为`卷积层-卷积层-池化层-dropout层-flatten层-全连接层-dropout层-softmax全连接层`,具体参数如下:
1. 第1层卷积，32个3x3的卷积核 ，激活函数使用 relu
2. 第2层卷积，64个3x3的卷积核，激活函数使用 relu
3. 最大池化层，池化窗口 2x2
4. Dropout 25% 的输入神经元
5. 将 Pooled feature map 摊平后输入全连接网络
6. 全联接层，激活函数使用 relu
7. Dropout 50% 的输入神经元
8. 使用 softmax 激活函数做多分类，输出各数字的概率

查看 MNIST CNN 模型网络结构:
```
________________________________________________________________
Layer (type)                 Output Shape              Param #   
=================================================================
conv2d_1 (Conv2D)            (None, 26, 26, 32)        320       
_________________________________________________________________
conv2d_2 (Conv2D)            (None, 24, 24, 64)        18496     
_________________________________________________________________
max_pooling2d_1 (MaxPooling2 (None, 12, 12, 64)        0         
_________________________________________________________________
dropout_1 (Dropout)          (None, 12, 12, 64)        0         
_________________________________________________________________
flatten_1 (Flatten)          (None, 9216)              0         
_________________________________________________________________
dense_1 (Dense)              (None, 128)               1179776   
_________________________________________________________________
dropout_2 (Dropout)          (None, 128)               0         
_________________________________________________________________
dense_2 (Dense)              (None, 10)                1290      
=================================================================
Total params: 1,199,882
Trainable params: 1,199,882
Non-trainable params: 0
```

```python

from keras.layers import Dense, Dropout, Flatten, Conv2D, MaxPooling2D
from keras import backend as K
import tensorflow as tf
import os
from keras.models import Sequential
from keras.utils import np_utils
import matplotlib.pyplot as plt
import numpy as np
from keras.datasets import mnist

# 获取数据集
(x_train, y_train), (x_test, y_test) = mnist.load_data()

# 规范化
img_rows, img_cols = 28, 28
if K.image_data_format() == 'channels_first':
    x_train = x_train.reshape(x_train.shape[0], 1, img_rows, img_cols)
    x_test = x_test.reshape(x_test.shape[0], 1, img_rows, img_cols)
    input_shape = (1, img_rows, img_cols)
else:
    x_train = x_train.reshape(x_train.shape[0], img_rows, img_cols, 1)
    x_test = x_test.reshape(x_test.shape[0], img_rows, img_cols, 1)
    input_shape = (img_rows, img_cols, 1)

X_train = x_train.astype('float32')
X_test = x_test.astype('float32')
X_train /= 255
X_test /= 255

# 统计各标签数量
label, count = np.unique(y_train, return_counts=True)
# print(label, count)

# 可视化标签数量
fig = plt.figure()
plt.bar(label, count, width=0.7, align='center')
plt.title("Label Distribution")
plt.xlabel("Label")
plt.ylabel("Count")
plt.xticks(label)
plt.ylim(0, 7500)
for label, count in zip(label, count):
    plt.text(label, count, '%d' % count, ha='center', va='bottom', fontsize=10)
# plt.show()

# one-hot编码
n_classes = 10
# print('before one-hot:', y_train.shape)
Y_train = np_utils.to_categorical(y_train, n_classes)
# print('after one-hot:', Y_train.shape)
Y_test = np_utils.to_categorical(y_test, n_classes)

# 使用 Keras sequential model 定义 MNIST CNN 网络
model = Sequential()
# 第1层卷积，32个3x3的卷积核 ，激活函数使用 relu
model.add(Conv2D(filters=32, kernel_size=(3, 3), activation='relu',
                 input_shape=input_shape))

# 第2层卷积，64个3x3的卷积核，激活函数使用 relu
model.add(Conv2D(filters=64, kernel_size=(3, 3), activation='relu'))

# 最大池化层，池化窗口 2x2
model.add(MaxPooling2D(pool_size=(2, 2)))

# Dropout 25% 的输入神经元
model.add(Dropout(0.25))

# 将 Pooled feature map 摊平后输入全连接网络
model.add(Flatten())

# 全联接层
model.add(Dense(128, activation='relu'))

# Dropout 50% 的输入神经元
model.add(Dropout(0.5))

# 使用 softmax 激活函数做多分类，输出各数字的概率
model.add(Dense(n_classes, activation='softmax'))

model.summary()

for layer in model.layers:
    print(layer.get_output_at(0).get_shape().as_list())

# 编译模型
model.compile(loss='categorical_crossentropy',
              metrics=['accuracy'], optimizer='adam')
# 训练模型
history = model.fit(
    X_train,
    Y_train,
    batch_size=128,
    epochs=5,
    verbose=2,
    validation_data=(X_test, Y_test)
)

# 可视化指标
# print(history.history)
fig = plt.figure()

plt.subplot(2, 1, 1)
plt.plot(history.history['accuracy'])
plt.plot(history.history['val_accuracy'])
plt.title('Model Accuracy')
plt.ylabel('accuracy')
plt.xlabel('epoch')
plt.legend(['train', 'test'], loc='lower right')

plt.subplot(2, 1, 2)
plt.plot(history.history['loss'])  # 损失
plt.plot(history.history['val_loss'])  # 测试集上的损失
plt.title('Model Loss')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend(['train', 'test'], loc='upper right')
plt.tight_layout()
plt.show()

# 保存模型
save_dir = "./mnist/model/"
if tf.io.gfile.exists(save_dir):
    tf.io.gfile.rmtree(save_dir)
tf.io.gfile.makedirs(save_dir)

model_name = 'keras_mnist.h5'
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)
```
利用cnn卷积神经网络训练后,可视化指标如下:
<img alt="手写数字3" src="https://i.loli.net/2019/12/04/xtWKq3zBeJHVr98.jpg" width="50%" />

#### 任务：验证码识别

**第1步:创建验证码数据集**

具体步骤如下:
1. 引入第三方包`ImageCaptcha`
2. 定义常量和字符集(验证码字符集(包括数字/大小字母/小写字母) 验证码参数(长度/高度/宽度) 数据集参数(训练数据集大小/测试数据集大小/训练数据集目录/测试数据集目录))
3. 定义生成随机字符的方法
4. 创建并保存验证码数据集的方法
5. 创建并保存训练集
6. 创建并保存测试集
7. 生成并返回验证码数据集的方法
8. 生成 100 张验证码图像和字符

```python
from captcha.image import ImageCaptcha
import random
import numpy as np
import matplotlib.pyplot as plt
import PIL.Image as Image
import tensorflow as tf

# 定义常量
NUMBER = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
LOWERCASE = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u',
             'v', 'w', 'x', 'y', 'z']
UPPERCASE = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U',
             'V', 'W', 'X', 'Y', 'Z']

CAPTCHA_CHARSET = NUMBER   # 验证码字符集
CAPTCHA_LEN = 4            # 验证码长度
CAPTCHA_HEIGHT = 60        # 验证码高度
CAPTCHA_WIDTH = 160        # 验证码宽度

TRAIN_DATASET_SIZE = 5000     # 验证码数据集大小
TEST_DATASET_SIZE = 1000
TRAIN_DATA_DIR = './train-data/'  # 验证码数据集目录
TEST_DATA_DIR = './test-data/'


# 生成随机字符
def gen_random_text(charset=CAPTCHA_CHARSET, length=CAPTCHA_LEN):
    text = [random.choice(charset) for _ in range(length)]
    return ''.join(text)

# 创建并保存验证码数据集


def create_captcha_dataset(
        size=100,
        data_dir='./data/',
        height=60,
        width=160,
        image_format='.png'):
    if tf.io.gfile.exists(data_dir):
        tf.io.gfile.rmtree(data_dir)
    tf.io.gfile.makedirs(data_dir)

    captcha = ImageCaptcha(width=width, height=height)

    for _ in range(size):
        text = gen_random_text(CAPTCHA_CHARSET, CAPTCHA_LEN)
        captcha.write(text, data_dir+text+image_format)

    return None


# 训练集
create_captcha_dataset(TRAIN_DATASET_SIZE, TRAIN_DATA_DIR)
# 测试集
create_captcha_dataset(TEST_DATASET_SIZE, TEST_DATA_DIR)


def gen_captcha_dataset(
        size=100,
        height=60,
        width=160,
        image_format='.png'):
    captcha = ImageCaptcha(width=width, height=height)
    images, texts = [None]*size, [None]*size

    for i in range(size):
        texts[i] = gen_random_text(CAPTCHA_CHARSET, CAPTCHA_LEN)
        images[i] = np.array(Image.open(captcha.generate(texts[i])))

    return images, texts


# 生成100张验证码图像
images, texts = gen_captcha_dataset()


# 可视化验证码前20张图片
plt.figure()
for i in range(20):
    plt.subplot(5, 4, i+1)
    plt.tight_layout()
    plt.imshow(images[i])
    plt.title("Label: {}".format(texts[i]))
    plt.xticks([])
    plt.yticks([])
plt.show()
```
最后生成 100 张验证码图像和字符如下:
<img alt="验证码1" src="https://i.loli.net/2019/12/04/cmGYqsn6NyEuL3r.jpg" width="50%" />

**第2步:数据处理**

具体步骤:
1. 读取训练集前 100 张图片，并通过文件名解析验证码（标签）
2. 数据可视化
3. 将 RGB 验证码图像转为灰度图
4. 数据规范化
5. 适配 Keras 图像数据格式
6. 对验证码中每个字符进行 one-hot 编码
7. 将验证码向量解码为对应字符

```python
from PIL import Image
from keras import backend as K
import random
import glob
import numpy as np
import matplotlib.pyplot as plt

NUMBER = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
LOWERCASE = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u',
             'v', 'w', 'x', 'y', 'z']
UPPERCASE = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U',
             'V', 'W', 'X', 'Y', 'Z']

CAPTCHA_CHARSET = NUMBER   # 验证码字符集
CAPTCHA_LEN = 4            # 验证码长度
CAPTCHA_HEIGHT = 60        # 验证码高度
CAPTCHA_WIDTH = 160        # 验证码宽度

TRAIN_DATA_DIR = './train-data/'  # 验证码数据集目录

# 读取训练集前100张图像
image = []
text = []
count = 0
for filename in glob.glob(TRAIN_DATA_DIR+'*.png'):
    image.append(np.array(Image.open(filename)))
    text.append(filename.lstrip(TRAIN_DATA_DIR).rstrip('.png')[1:])
    count += 1
    if count >= 100:
        break

# 数据可视化
# plt.figure()
# for i in range(20):
#     plt.subplot(5, 4, i+1)
#     plt.tight_layout()
#     plt.imshow(image[i])
#     plt.title("Label: {}".format(text[i]))
#     plt.xticks([])
#     plt.yticks([])
# plt.show()

image = np.array(image, dtype=np.float32)
# print(image.shape)  # (100, 60, 160, 3)

# 将RGB转化为灰度图

def rgb2grey(img):
    return np.dot(img[..., :3], [0.299, 0.587, 0.114])

image = rgb2grey(image)
# print(image.shape)  # (100, 60, 160)

# 数据可视化
# plt.figure()
# for i in range(20):
#     plt.subplot(5, 4, i+1)
#     plt.tight_layout()
#     plt.imshow(image[i], cmap='Greys')
#     plt.title("Label: {}".format(text[i]))
#     plt.xticks([])
#     plt.yticks([])
# plt.show()

# 数据规范化
image = image/255
# 适配keras图像数据格式


def fit_keras_channels(batch, rows=CAPTCHA_HEIGHT, cols=CAPTCHA_WIDTH):
    if K.image_data_format() == 'channels_first':
        batch = batch.reshape(batch.shape[0], 1, rows, cols)
        input_shape = (1, rows, cols)
    else:
        batch = batch.reshape(batch.shape[0], rows, cols, 1)
        input_shape = (rows, cols, 1)
    return batch, input_shape


image, input_shape = fit_keras_channels(image)
# print(image.shape)  # (100, 60, 160, 1)
# print(input_shape)  # (60, 160, 1)


# 对验证码中每个字符进行 one-hot 编码
def text2vec(text, length=CAPTCHA_LEN, charset=CAPTCHA_CHARSET):
    text_len = len(text)
    # 验证码长度校验
    if text_len != length:
        raise ValueError(
            'Error: length of captcha should be {}, but got {}'.format(length, text_len))

    # 生成一个形如（CAPTCHA_LEN*CAPTHA_CHARSET,) 的一维向量
    # 例如，4个纯数字的验证码生成形如(4*10,)的一维向量
    vec = np.zeros(length * len(charset))
    for i in range(length):
        # One-hot 编码验证码中的每个数字
        # 每个字符的热码 = 索引 + 偏移量
        vec[charset.index(text[i]) + i*len(charset)] = 1
    return vec


text = list(text)
vec = [None]*len(text)
for i in range(len(vec)):
    vec[i] = text2vec(text[i])

# 将验证码向量解码为对应字符
def vec2text(vector):
    if not isinstance(vector, np.ndarray):
        vector = np.asarray(vector)
    vector = np.reshape(vector, [CAPTCHA_LEN, -1])
    text = ''
    for item in vector:
        text += CAPTCHA_CHARSET[np.argmax(item)]
    return text
```
最后生成的数字灰度图效果如下:
------------------验证码识别2------------------

**第3步:训练模型**

具体步骤(前6步准备工作已经做过了,主要是要进行7-15步的模型训练过程,16-17步是做对应的保存工作):
1. 引入第三方包
2. 定义超参数和字符集
3. 将 RGB 验证码图像转为灰度图
4. 对验证码中每个字符进行 one-hot 编码
5. 将验证码向量解码为对应字符
6. 适配 Keras 图像数据格式
7. 读取训练集
8. 处理训练集图像
9. 处理训练集标签
10. 读取测试集，处理对应图像和标签
11. 创建验证码识别模型
12. 查看模型摘要
13. 模型可视化
14. 训练模型
15. 预测样例
16. 保存模型
17. 保存训练过程记录

```python
from PIL import Image
from keras import backend as K
from keras.utils.vis_utils import plot_model
from keras.models import *
from keras.layers import *
import glob
import pickle
import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf


NUMBER = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']
LOWERCASE = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u',
             'v', 'w', 'x', 'y', 'z']
UPPERCASE = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U',
             'V', 'W', 'X', 'Y', 'Z']

CAPTCHA_CHARSET = NUMBER   # 验证码字符集
CAPTCHA_LEN = 4            # 验证码长度
CAPTCHA_HEIGHT = 60        # 验证码高度
CAPTCHA_WIDTH = 160        # 验证码宽度

TRAIN_DATA_DIR = './train-data/'  # 验证码数据集目录
TEST_DATA_DIR = './test-data/'

BATCH_SIZE = 100
EPOCHS = 10
OPT = 'adam'
LOSS = 'binary_crossentropy'

MODEL_DIR = './model/train_demo/'
MODEL_FORMAT = '.h5'
HISTORY_DIR = './history/train_demo/'
HISTORY_FORMAT = '.history'

filename_str = "{}captcha_{}_{}_bs_{}_epochs_{}{}"

# 模型网络结构文件
MODEL_VIS_FILE = 'captcha_classfication' + '.png'
# 模型文件
MODEL_FILE = filename_str.format(
    MODEL_DIR, OPT, LOSS, str(BATCH_SIZE), str(EPOCHS), MODEL_FORMAT)
# 训练记录文件
HISTORY_FILE = filename_str.format(
    HISTORY_DIR, OPT, LOSS, str(BATCH_SIZE), str(EPOCHS), HISTORY_FORMAT)


def rgb2gray(img):
    return np.dot(img[..., :3], [0.299, 0.587, 0.114])


# 对验证码中每个字符进行 one-hot 编码


def text2vec(text, length=CAPTCHA_LEN, charset=CAPTCHA_CHARSET):
    text_len = len(text)
    # 验证码长度校验
    if text_len != length:
        raise ValueError(
            'Error: length of captcha should be {}, but got {}'.format(length, text_len))

    # 生成一个形如（CAPTCHA_LEN*CAPTHA_CHARSET,) 的一维向量
    # 例如，4个纯数字的验证码生成形如(4*10,)的一维向量
    vec = np.zeros(length * len(charset))
    for i in range(length):
        # One-hot 编码验证码中的每个数字
        # 每个字符的热码 = 索引 + 偏移量
        vec[charset.index(text[i]) + i*len(charset)] = 1
    return vec

# 将验证码向量解码为对应字符


def vec2text(vector):
    if not isinstance(vector, np.ndarray):
        vector = np.asarray(vector)
    vector = np.reshape(vector, [CAPTCHA_LEN, -1])
    text = ''
    for item in vector:
        text += CAPTCHA_CHARSET[np.argmax(item)]
    return text


def fit_keras_channels(batch, rows=CAPTCHA_HEIGHT, cols=CAPTCHA_WIDTH):
    if K.image_data_format() == 'channels_first':
        batch = batch.reshape(batch.shape[0], 1, rows, cols)
        input_shape = (1, rows, cols)
    else:
        batch = batch.reshape(batch.shape[0], rows, cols, 1)
        input_shape = (rows, cols, 1)
    return batch, input_shape

# 读取训练集


X_train = []
Y_train = []
for filename in glob.glob(TRAIN_DATA_DIR + '*.png'):
    X_train.append(np.array(Image.open(filename)))
    Y_train.append(filename.lstrip(TRAIN_DATA_DIR).rstrip('.png')[1:])
X_train = np.array(X_train, dtype=np.float32)
X_train = rgb2gray(X_train)
X_train = X_train / 255
X_train, input_shape = fit_keras_channels(X_train)
# (3948, 60, 160, 1) <class 'numpy.ndarray'>
print(X_train.shape, type(X_train))
print(input_shape)  # (60, 160, 1)

# 处理训练集标签
Y_train = list(Y_train)
for i in range(len(Y_train)):
    Y_train[i] = text2vec(Y_train[i])
Y_train = np.asarray(Y_train)
print(Y_train.shape, type(Y_train))


# 读取测试集，处理对应图像和标签
X_test = []
Y_test = []
for filename in glob.glob(TEST_DATA_DIR + '*.png'):
    X_test.append(np.array(Image.open(filename)))
    Y_test.append(filename.lstrip(TEST_DATA_DIR).rstrip('.png')[1:])
# list -> rgb -> gray -> normalization -> fit keras
X_test = np.array(X_test, dtype=np.float32)
X_test = rgb2gray(X_test)
X_test = X_test / 255
X_test, _ = fit_keras_channels(X_test)
Y_test = list(Y_test)
for i in range(len(Y_test)):
    Y_test[i] = text2vec(Y_test[i])
Y_test = np.asarray(Y_test)
print(X_test.shape, type(X_test))
print(Y_test.shape, type(Y_test))


# 创建验证码识别模型
inputs = Input(shape=input_shape, name="inputs")
# 第1层卷积
conv1 = Conv2D(32, (3, 3), name="conv1")(inputs)
relu1 = Activation('relu', name="relu1")(conv1)

# 第2层卷积
conv2 = Conv2D(32, (3, 3), name="conv2")(relu1)
relu2 = Activation('relu', name="relu2")(conv2)
pool2 = MaxPooling2D(pool_size=(2, 2), padding='same', name="pool2")(relu2)

# 第3层卷积
conv3 = Conv2D(64, (3, 3), name="conv3")(pool2)
relu3 = Activation('relu', name="relu3")(conv3)
pool3 = MaxPooling2D(pool_size=(2, 2), padding='same', name="pool3")(relu3)

# 将 Pooled feature map 摊平后输入全连接网络
x = Flatten()(pool3)

# Dropout
x = Dropout(0.25)(x)

# 4个全连接层分别做10分类，分别对应4个字符。
x = [Dense(10, activation='softmax', name='fc%d' % (i+1))(x) for i in range(4)]

# 4个字符向量拼接在一起，与标签向量形式一致，作为模型输出。
outs = Concatenate()(x)

# 定义模型的输入与输出
model = Model(inputs=inputs, outputs=outs)
model.compile(optimizer=OPT, loss=LOSS, metrics=['accuracy'])

model.summary()
plot_model(model, to_file=MODEL_VIS_FILE, show_shapes=True)
history = model.fit(X_train,
                    Y_train,
                    batch_size=BATCH_SIZE,
                    epochs=EPOCHS,
                    verbose=2,
                    validation_data=(X_test, Y_test))

print(vec2text(Y_test[9]))
yy = model.predict(X_test[9].reshape(1, 60, 160, 1))
print(vec2text(yy))

if not tf.io.gfile.exists(MODEL_DIR):
    tf.io.gfile.makedirs(MODEL_DIR)
model.save(MODEL_DIR)
print('Saved trained model at %s ' % MODEL_FILE)
```

训练模型得到的参数如下:
```

Train on 3956 samples, validate on 954 samples
Epoch 1/10
 - 149s - loss: 0.3270 - acc: 0.9000 - val_loss: 0.3247 - val_acc: 0.9000
Epoch 2/10
 - 122s - loss: 0.3229 - acc: 0.9000 - val_loss: 0.3195 - val_acc: 0.9000
Epoch 3/10
 - 114s - loss: 0.2987 - acc: 0.9004 - val_loss: 0.2726 - val_acc: 0.9028
Epoch 4/10
 - 106s - loss: 0.2257 - acc: 0.9164 - val_loss: 0.2303 - val_acc: 0.9171
Epoch 5/10
 - 103s - loss: 0.1799 - acc: 0.9337 - val_loss: 0.2171 - val_acc: 0.9209
Epoch 6/10
 - 113s - loss: 0.1523 - acc: 0.9447 - val_loss: 0.2062 - val_acc: 0.9254
Epoch 7/10
 - 112s - loss: 0.1383 - acc: 0.9498 - val_loss: 0.2048 - val_acc: 0.9260
Epoch 8/10
 - 127s - loss: 0.1251 - acc: 0.9550 - val_loss: 0.2052 - val_acc: 0.9260
Epoch 9/10
 - 161s - loss: 0.1144 - acc: 0.9587 - val_loss: 0.2013 - val_acc: 0.9285
Epoch 10/10
 - 159s - loss: 0.1063 - acc: 0.9618 - val_loss: 0.2045 - val_acc: 0.9268
```

#### 任务：实现词云
要实现词云效果，首先需要安装wordcloud库，`pip install wordcloud`进行安装即可。
**1. 英文词云**

实现基本的英文词云：
```python
# -*- coding: utf-8 -*-

from wordcloud import WordCloud
import matplotlib.pyplot as plt

# 打开文本
text = open('./text/constitution.txt').read()
# 生成对象
wc = WordCloud().generate(text)

# 显示词云
plt.imshow(wc, interpolation='bilinear')
plt.axis('off')
plt.show()

# 保存到文件
wc.to_file('./img/word1.png')
```
效果展示：
------------------word1.png-------------------------
**2. 中文词云**
```python
# -*- coding: utf-8 -*-

from wordcloud import WordCloud
import matplotlib.pyplot as plt

# 打开文本
text = open('./text/xyj.txt',encoding='UTF-8').read()
# 生成对象
wc = WordCloud(font_path='Hiragino.ttf', width=800, height=600, mode='RGBA', background_color=None).generate(text)

# 显示词云
plt.imshow(wc, interpolation='bilinear')
plt.axis('off')
plt.show()

# 保存到文件
wc.to_file('./img/word2.png')
```
其中读取文件使用`text = open('xxx.txt',encoding='UTF-8').read()`，注意这里要写`encoding='UTF-8'`，否则无法正确读取内容。要额外引入字体文件，如上例引入了`Hiragino.ttf`字体。最后生成的效果如下，但是有个问题就是每个词并不是按照中文意思进行断开的，无实际意义，接下来我们处理中文分词问题。
------------------word2.png-------------------------
**3. 中文词云+分词**
```python
# -*- coding: utf-8 -*-

from wordcloud import WordCloud
import matplotlib.pyplot as plt
import jieba

# 打开文本
text = open('./text/xyj.txt',encoding='UTF-8').read()

# 中文分词
text=' '.join(jieba.cut(text))

# 生成对象
wc = WordCloud(font_path='Hiragino.ttf', width=800, height=600, mode='RGBA', background_color=None).generate(text)

# 显示词云
plt.imshow(wc, interpolation='bilinear')
plt.axis('off')
plt.show()

# 保存到文件
wc.to_file('./img/word3.png')
```
因为英文文章中每个单词都是使用空格隔开的，因此不需要手动分词，而中文文章每个词是连在一起的，需要引入第三方包jieba进行中文分词操作。核心代码是`text=' '.join(jieba.cut(text))`，这样可以使得每个独立的词用空格隔开。最后生成的效果如下：
------------------word3.png-------------------------
**4. 中文词云+分词+黑白蒙版**
```python
# -*- coding: utf-8 -*-

from wordcloud import WordCloud
import matplotlib.pyplot as plt
from PIL import Image
import numpy as np
import jieba

# 打开文本
text = open('./text/xyj.txt',encoding='UTF-8').read()

# 中文分词
text=' '.join(jieba.cut(text))

# 启用黑白蒙版
mask=np.array(Image.open('./mask/black_mask.png'))
wc = WordCloud(mask=mask,font_path='Hiragino.ttf', width=800, height=600, mode='RGBA', background_color=None).generate(text)

# 显示词云
plt.imshow(wc, interpolation='bilinear')
plt.axis('off')
plt.show()

# 保存到文件
wc.to_file('./img/word4.png')
```
这里使用了黑白图片作为蒙版，WordCloud()函数中传入mask参数，则可以启用对应的蒙版，这样生成的词云会与蒙版的图形相同。效果如下：
------------------black_mask.png-------------------------
------------------word4.png-------------------------
**5. 中文词云+分词+彩色蒙版**
```python
# -*- coding: utf-8 -*-

from wordcloud import WordCloud,ImageColorGenerator
import matplotlib.pyplot as plt
from PIL import Image
import numpy as np
import jieba

# 打开文本
text = open('./text/xyj.txt',encoding='UTF-8').read()

# 中文分词
text=' '.join(jieba.cut(text))

# 启用彩色蒙版
mask=np.array(Image.open('./mask/color_mask.png'))
wc = WordCloud(mask=mask,font_path='Hiragino.ttf', width=800, height=600, mode='RGBA', background_color=None).generate(text)

# 从图片中生成颜色
image_colors=ImageColorGenerator(mask)
wc.recolor(color_func=image_colors)

# 显示词云
plt.imshow(wc, interpolation='bilinear')
plt.axis('off')
plt.show()

# 保存到文件
wc.to_file('./img/word5.png')
```
这里使用了彩色图片作为蒙版，从`wordcloud`引入`ImageColorGenerator`函数，从图片中生成颜色，这样生成的词云颜色和蒙版的颜色是相同的（每个部分的颜色都是大致对应的），效果如下：
------------------color_mask.png-------------------------
------------------word5.png-------------------------
**6. 中文词云+分词+彩色蒙版+自定义颜色**
```python
# -*- coding: utf-8 -*-

from wordcloud import WordCloud
import matplotlib.pyplot as plt
from PIL import Image
import numpy as np
import random
import jieba

# 打开文本
text = open('./text/xyj.txt',encoding='UTF-8').read()

# 中文分词
text=' '.join(jieba.cut(text))

# 自定义颜色函数
def random_color(word, font_size, position, orientation, font_path, random_state):
	s = 'hsl(0, %d%%, %d%%)' % (random.randint(60, 80), random.randint(60, 80))
	return s

# 启用彩色蒙版
mask=np.array(Image.open('./mask/color_mask.png'))
wc = WordCloud(color_func=random_color,mask=mask,font_path='Hiragino.ttf', width=800, height=600, mode='RGBA', background_color=None).generate(text)

# 显示词云
plt.imshow(wc, interpolation='bilinear')
plt.axis('off')
plt.show()

# 保存到文件
wc.to_file('./img/word6.png')
```
为了实现为词云自定义颜色，我们可以单独实现一个上色函数random_color，在WordCloud()函数中传入color_func参数即可，效果如下：
------------------word6.png-------------------------
**7. 中文词云+分词+彩色蒙版+关键词权重**
```python
# -*- coding: utf-8 -*-

from wordcloud import WordCloud,ImageColorGenerator
import matplotlib.pyplot as plt
from PIL import Image
import numpy as np
import jieba.analyse

# 打开文本
text = open('./text/xyj.txt',encoding='UTF-8').read()

# 提取关键词和权重
freq=jieba.analyse.extract_tags(text,topK=200,withWeight=True)
freq = {i[0]: i[1] for i in freq}

# 中文分词
text=' '.join(jieba.cut(text))

# 启用彩色蒙版
mask=np.array(Image.open('./mask/color_mask.png'))
wc = WordCloud(mask=mask,font_path='Hiragino.ttf', width=800, height=600, mode='RGBA', background_color=None).generate_from_frequencies(freq)

# 从图片中生成颜色
image_colors=ImageColorGenerator(mask)
wc.recolor(color_func=image_colors)

# 显示词云
plt.imshow(wc, interpolation='bilinear')
plt.axis('off')
plt.show()

# 保存到文件
wc.to_file('./img/word7.png')
```
为了在词云中凸显词语出现的频率，我们可以采用根据频率上色的方法，频率出现越高则着色越深。先提取关键词和权重，再通过`WordCloud().generate_from_frequencies(freq)`生成词云即可，注意这里的freq是词与频次关系的字典。最后生成的效果如下：
------------------word7.png-------------------------

#### 任务：图像风格迁移