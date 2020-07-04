---
title: tensorflow入门-2
date: 2020-04-04
categories: AI
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

核心内容：以下介绍tensorflow的进阶使用(以tensorflow v2.0为例)。如果上手机器学习直接接触的是tensorflow v2.0，则没有必要去学习tensorflow v1的版本。tensorflow v2.0极大地简化了使用，对用户非常友好。

<!-- more -->

具体教程详见[最全Tensorflow2.0 入门教程持续更新](https://zhuanlan.zhihu.com/p/59507137)

### 一、tensorflow2.0安装
Tensorflow2.0可以直接用pip安装:`pip install tensorflow==2.0.0-alpha0`
如果速度比较慢，可以换国内清华源， 在终端执行下面命令：
```python
# 升级 pip 到最新的版本 (>=10.0.0) 后进行配置：
pip install pip -U
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```
然后重新运行安装命令：`pip install tensorflow==2.0.0-alpha0` ,这样，就可以开始使用tensorflow 2.0了

### 二、tensorflow2.0使用
#### Keras 快速入门
**tf.keras是tensorflow2.0的核心高阶API。**
Keras 是一个用于构建和训练深度学习模型的高阶 API。它可用于快速设计原型、高级研究和生产。
**tensorflow2推荐使用keras构建网络**，常见的神经网络都包含在keras.layer中(最新的tf.keras的版本可能和keras不同)

**训练的6部曲**
- import
- train, test
- model=tf.keras.models.Sequential()
- model.compile()
- model.fit
- model.summary(非必须)

**导入tf.keras**
```python
import tensorflow as tf
from tensorflow import keras
print(tf.__version__)
print(keras.__version__)
```
**构建模型**
1. 直接堆叠构建模型

```python
# 直接叠加层构建模型
model = tf.keras.Sequential()
model.add(layers.Dense(32, activation='relu'))
model.add(layers.Dense(32, activation='relu'))
model.add(layers.Dense(10, activation='softmax'))

# 使用函数api构建模型
input_x = tf.keras.Input(shape=(72,))
hidden1 = layers.Dense(32, activation='relu')(input_x)
hidden2 = layers.Dense(16, activation='relu')(hidden1)
pred = layers.Dense(10, activation='softmax')(hidden2)
model = tf.keras.Model(inputs=input_x, outputs=pred)
```
2. 使用Sequential批量构建模型

```python
model=tf.keras.models.Sequential([
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(128,activation='relu'),
    tf.keras.layers.Dense(10,activation='softmax')
])
```
3. 使用class构建模型

```python
class MnistModel(Model):
    def __init__(self):
        super(MnistModel,self).__init__()
        self.flatten=Flatten()
        self.d1=Dense(128,activation='relu')
        self.d2=Dense(10,activation='softmax')
    def call(self,x):
        x=self.flatten(x)
        x=self.d1(x)
        y=self.d2(x)
        return y
model=MnistModel()
```

**tf.keras.layers中layer配置：**
- activation：设置层的激活函数。此参数由内置函数的名称指定，或指定为可调用对象。默认情况下，系统不会应用任何激活函数。
- kernel_initializer 和 bias_initializer：创建层权重（核和偏差）的初始化方案。此参数是一个名称或可调用对象，默认为 "Glorot uniform" 初始化器。
- kernel_regularizer 和 bias_regularizer：应用层权重（核和偏差）的正则化方案，例如 L1 或 L2 正则化。默认情况下，系统不会应用正则化函数。
如:

```python
layers.Dense(32, activation='sigmoid')
layers.Dense(32, activation=tf.sigmoid)
layers.Dense(32, kernel_initializer='orthogonal')
layers.Dense(32, kernel_initializer=tf.keras.initializers.glorot_normal)
layers.Dense(32, kernel_regularizer=tf.keras.regularizers.l2(0.01))
layers.Dense(32, kernel_regularizer=tf.keras.regularizers.l1(0.01))
```

**设置训练流程**
```python
model.compile(
    optimizer='adam',
    loss=tf.keras.losses.SparseCategoricalCrossentropy(from_logits=False),
    metrics=['sparse_categorical_accuracy']
)
```
**开始训练**
`model.fit(train_x, train_y, batch_size=32, epochs=5)`

**其他:**
- **自定义层**
通过对 tf.keras.layers.Layer 进行子类化并实现以下方法来创建自定义层, 其中, build是创建层的权重, call是定义前向传播, compute_output_shape指定在给定输入形状的情况下如何计算层的输出形状.
```python
class MyDense(tf.keras.layers.Layer):
    def __init__(self, n_outputs):
        super(MyDense, self).__init__()
        self.n_outputs = n_outputs
    
    def build(self, input_shape):
        self.kernel = self.add_variable('kernel',shape=[int(input_shape[-1]),self.n_outputs])
    def call(self, input):
        return tf.matmul(input, self.kernel)
layer = MyDense(10)
# print(layer(tf.ones([6, 5])))
# print(layer.trainable_variables)
```
- **回调**
常见的回调函数有EarlyStopping、TensorBoard、ModelCheckpoint等等, 具体详见https://tensorflow.google.cn/api_docs/python/tf/keras/callbacks
```python
callbacks = [
    tf.keras.callbacks.EarlyStopping(patience=2, monitor='val_loss'),
    tf.keras.callbacks.TensorBoard(log_dir='./logs')
]
model.fit(train_x, train_y, batch_size=16, epochs=5,
         callbacks=callbacks, validation_data=(val_x, val_y))
```
- **模型保存**
```python
这里加入保存模型的代码
checkpoint_save_path='./checkpoint/fashion_mnist.ckpt'
if os.path.exists(checkpoint_save_path+'.index'):
    print('-----------load model_____________')
    model.load_weights(checkpoint_save_path)

cp_callback=tf.keras.callbacks.ModelCheckpoint(
    filepath=checkpoint_save_path,
    save_weights_only=True,
    save_best_only=True
)
```
- **权重保存**
```python
# 将模型参数存入文本
# print(model.trainable_variables)
file=open('./weights.txt','w')
for v in model.trainable_variables:
    file.write(str(v.name)+'\n')
    file.write(str(v.shape)+'\n')
    file.write(str(v.numpy())+'\n')
file.close()
```
 **图片增强**
图片增强. 提高模型的鲁棒性.
```python
# 这里新增图片增强部分
image_gen_train=ImageDataGenerator(
    rescale=1./1.,
    rotation_range=45,
    width_shift_range=0.15,
    height_shift_range=0.15,
    horizontal_flip=True,
    zoom_range=0.5
)
image_gen_train.fit(x_train)

# ...中间定义模型model

history=model.fit(
    image_gen_train.flow(x_train,y_train,batch_size=32),#这里注意使用image_gen_train.flow()
    epochs=5,
    validation_data=(x_test,y_test),
    validation_freq=1
)
```
#### keras 函数api
1. 构建简单的网络
- 创建网络
- 训练、验证及测试
- 模型保存和序列化
2.  使用共享网络创建多个模型
- 在函数API中，通过在图层图中指定其输入和输出来创建模型。 这意味着可以使用单个图层图来生成多个模型。可以把整个模型，当作一层网络使用。
3. 复杂网络结构构建
- 多输入与多输出网络
- 小型残差网络
4. 共享网络层
5. 模型复用
6. 自定义网络层

#### 使用keras训练模型
1. 一般的模型构造、训练、测试流程
2. 自定义损失和指标
3. 使用tf.data构造数据
4. 样本权重和类权重
5. 多输入多输出模型
6. 使用回调

Keras中的回调是在训练期间（在epoch开始时，batch结束时，epoch结束时等）在不同点调用的对象，可用于实现以下行为：
- 在培训期间的不同时间点进行验证（超出内置的每个时期验证）
- 定期检查模型或超过某个精度阈值
- 在训练似乎平稳时改变模型的学习率
- 在训练似乎平稳时对顶层进行微调
- 在培训结束或超出某个性能阈值时发送电子邮件或即时消息通知等等。
**可使用的内置回调有**
- ModelCheckpoint：定期保存模型。
- EarlyStopping：当训练不再改进验证指标时停止培训。
- TensorBoard：定期编写可在TensorBoard中显示的模型日志（更多细节见“可视化”）。
- CSVLogger：将丢失和指标数据流式传输到CSV文件。
- ...
还可以创建自己的回调方法.
7. 自己构造训练和验证循环

#### 用keras构建自己的网络层
1. 构建一个简单的网络层
2. 使用子层递归构建网络层
3. 其他网络层配置
4. 构建自己的模型

#### keras模型保存和序列化
1. 保存全模型
可以对整个模型进行保存，其保存的内容包括：
- 该模型的架构
- 模型的权重（在训练期间学到的）
- 模型的训练配置（你传递给编译的）
- 优化器及其状态（可以从中断的地方重新启动训练）
```python
import numpy as np
model.save('the_save_model.h5')
new_model = keras.models.load_model('the_save_model.h5')
new_prediction = new_model.predict(x_test)
np.testing.assert_allclose(predictions, new_prediction, atol=1e-6) # 预测结果一样
```
2. 保存为SavedModel文件
3. 仅保存网络结构
4. 仅保存网络参数
5. 完整的模型保存方法
6. 保存网络权重为SavedModel格式
7. 子类模型参数保存

### 三、tensorflow2.0基础网络结构
#### 基础MLP网络
**回归任务**
```python
# 回归任务
import tensorflow as tf
import tensorflow.keras as keras
import tensorflow.keras.layers as layers
import matplotlib.pyplot as plt

(x_train, y_train), (x_test, y_test) = keras.datasets.boston_housing.load_data()
# print(x_train.shape, ' ', y_train.shape)
# print(x_test.shape, ' ', y_test.shape)
model = keras.Sequential([
    layers.Dense(32, activation='sigmoid', input_shape=(13,)),
    layers.Dense(32, activation='sigmoid'),
    layers.Dense(32, activation='sigmoid'),
    layers.Dense(1)
])
model.compile(optimizer=keras.optimizers.SGD(0.1),
             loss='mean_squared_error',  # keras.losses.mean_squared_error
             metrics=['mse'])
# model.summary()
history =model.fit(x_train, y_train, batch_size=50, epochs=50, validation_split=0.1, verbose=1)

result = model.evaluate(x_test, y_test)
print(result)
```
**分类任务**
```python
# 分类任务
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split

whole_data = load_breast_cancer()
x_data = whole_data.data
y_data = whole_data.target
x_train, x_test, y_train, y_test = train_test_split(x_data, y_data, test_size=0.3, random_state=7)

# 构建模型
model = keras.Sequential([
    layers.Dense(32, activation='relu', input_shape=(30,)),
    layers.Dense(32, activation='relu'),
    layers.Dense(1, activation='sigmoid')
])

model.compile(
    optimizer=keras.optimizers.Adam(),
    loss=keras.losses.binary_crossentropy,
    metrics=['accuracy']
)
model.fit(x_train, y_train, batch_size=64, epochs=10, verbose=1)
model.evaluate(x_test, y_test)
print(model.metrics_names)
```
#### MLP及深度学习常见技巧
1. 权重初始化方案
2. 激活函数
3. 优化器
4. 批正则化BN
5. dropout
6. .模型集成

#### 基础CNN网络

```python
model = keras.Sequential()
# 卷积层
model.add(layers.Conv2D(input_shape=(x_train.shape[1], x_train.shape[2], x_train.shape[3]),
                        filters=32, kernel_size=(3,3), strides=(1,1), padding='valid',
                       activation='relu'))
# 池化层
model.add(layers.MaxPool2D(pool_size=(2,2)))
# 全连接层
model.add(layers.Flatten())
model.add(layers.Dense(32, activation='relu'))
# softmax层
model.add(layers.Dense(10, activation='softmax'))
```

#### CNN变体网络
**AlexNet**

```python
deep_model = keras.Sequential([
    layers.Conv2D(input_shape=((x_shape[1], x_shape[2], x_shape[3])),
                 filters=32, kernel_size=(3,3), strides=(1,1), padding='same', activation='relu'),
    layers.Conv2D(filters=32, kernel_size=(3,3), strides=(1,1), padding='same', activation='relu'),
    layers.MaxPool2D(pool_size=(2,2)),
    layers.Conv2D(filters=32, kernel_size=(3,3), strides=(1,1), padding='same', activation='relu'),
    layers.Conv2D(filters=32, kernel_size=(3,3), strides=(1,1), padding='same', activation='relu'),
    layers.MaxPool2D(pool_size=(2,2)),
    layers.Flatten(),
    layers.Dense(32, activation='relu'),
    layers.Dense(10, activation='softmax')
])
```
**添加了其它功能层的深度卷积**

```python
deep_model = keras.Sequential([
    layers.Conv2D(input_shape=((x_shape[1], x_shape[2], x_shape[3])),
                 filters=32, kernel_size=(3,3), strides=(1,1), padding='same', activation='relu'),
    layers.BatchNormalization(),
    layers.Conv2D(filters=32, kernel_size=(3,3), strides=(1,1), padding='same', activation='relu'),
    layers.BatchNormalization(),
    layers.MaxPool2D(pool_size=(2,2)),
    layers.Conv2D(filters=32, kernel_size=(3,3), strides=(1,1), padding='same', activation='relu'),
    layers.BatchNormalization(),
    layers.BatchNormalization(),
    layers.Conv2D(filters=32, kernel_size=(3,3), strides=(1,1), padding='same', activation='relu'),
    layers.MaxPool2D(pool_size=(2,2)),
    layers.Flatten(),
    layers.Dense(32, activation='relu'),
    layers.Dropout(0.2),
    layers.Dense(10, activation='softmax')
])
```
**NIN网络 (GoogleNet 中就用到了NIN结构)**

```python
deep_model = keras.Sequential([
    layers.Conv2D(input_shape=((x_shape[1], x_shape[2], x_shape[3])),
                 filters=32, kernel_size=(3,3), strides=(1,1), padding='same', activation='relu'),
    layers.BatchNormalization(),
    layers.Conv2D(filters=16, kernel_size=(1,1), strides=(1,1), padding='valid', activation='relu'),
    layers.BatchNormalization(),
    layers.MaxPool2D(pool_size=(2,2)),
    layers.Conv2D(filters=32, kernel_size=(3,3), strides=(1,1), padding='same', activation='relu'),
    layers.BatchNormalization(),
    layers.Conv2D(filters=16, kernel_size=(1,1), strides=(1,1), padding='valid', activation='relu'),
    layers.BatchNormalization(),
    layers.MaxPool2D(pool_size=(2,2)),
    layers.Flatten(),
    layers.Dense(32, activation='relu'),
    layers.Dropout(0.2),
    layers.Dense(10, activation='softmax')
])
```
#### 文本卷积
#### LSTM和GRU
#### 自编码器
#### 卷积自编码器
#### 词嵌入
#### DCGAN
#### 使用Estimator构建Boosted trees
#### RNN文本分类
#### Transformer

### 五、eager模式
众所周知，Tensorflow入门之所以困难，与其采用的Graph 和 Session 模式有关，这与原生的 Python 代码简单、直观的印象格格不入。同时，由于计算仅仅发生在Session里面，所以初始化参数和变量的时候没办法将结果打印出来，以至于调试起来也十分困难。

当然Google官方也意识到了这点，于是引入了Eager模式，在这个模式下tensorflow的常量和变量可以直接计算并打印出来，甚至还可以和numpy数组混合计算。

运行tensorflow程序，需要导入tensorflow模块。 从TensorFlow 2.0开始，默认情况下会启用Eager模式执行。TensorFlow 的 Eager 模式是一个命令式、由运行定义的接口，一旦从 Python 被调用，其操作立即被执行 ，无需事先构建静态图。这为TensorFlow提供了一个更具交互性能的前端。
#### 张量极其操作
#### 自定义层
#### 自动求导
#### 使用低级api训练(非tf.keras)
#### 自定义训练实战(非tf.keras)
#### TF fuction和AutoGraph

### 六、tensorflow.js
#### 如何安装
Tensorflow.js Converter 依赖**python3.6.8**版本

**搭建虚拟环境：**
1. 安装conda
可以使用[清华镜像源](https://mirror.tuna.tsinghua.edu.cn/help/anaconda/)，安装Miniconda 即可（注意配置环境变量）
2. 在终端检查conda命令是否可用，并创建指定python版本的虚拟环境
`conda create -n [name] python=3.6.8` 创建虚拟环境
`conda remove -n [name] --all` 删除虚拟环境
`conda info --envs` 查看虚拟环境
`conda activate [name]` 激活虚拟环境（可以发现python版本已经改变）
`conda deactivate [name]` 退出虚拟环境
3. 安装tfjs converter
`pip install tensorflowjs`安装
`tensorflowjs_converter -h`检查是否安装成功

#### python与js模型的互转
1. 准备工作
`conda activate [name]`激活
`tensorflowjs_converter`检查
2. 开始转换——具体格式建议看文档（github tfjs-converter）
  1）python模型转js模型
  `tensorflowjs_converter --input_format=keras --output_format=tf_layers_model [input_path] [output_path]`
  2）js模型转python模型
  `tensorflowjs_converter --input_format=tf_layers_model --output_format=keras [input_path] [output_path]`
3. 验证模型正确性

#### 模型加速
1. 分片(--weight_shared_size_bytes=[size])
 `tensorflowjs_converter --input_format=tf_layers_model --output_format=tf_layers_model --weight_shared_size_bytes=100000 [input_path] [output_path]`
在输出文件夹中会发现很多块分片后的模型，这样在加载模型时可以使用并发实现加速
2. 量化(--quantization_bytes=[n])
 `tensorflowjs_converter --input_format=tf_layers_model --output_format=tf_layers_model --quantization_bytes=2 [input_path] [output_path]`
3. 通过转为tfjs_graph_model来加速模型——内部实现了凸优化(--output_format=tf_graph_model)
  `tensorflowjs_converter --input_format=tf_layers_model --output_format=tf_graph_model [input_path] [output_path]`