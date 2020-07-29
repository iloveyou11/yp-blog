---
title: NLP模型-16-受限玻尔兹曼机
date: 2020-07-28
categories: AI
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

[NLP模型-01-预训练语言模型](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-01-%E9%A2%84%E8%AE%AD%E7%BB%83%E8%AF%AD%E8%A8%80%E6%A8%A1%E5%9E%8B/)
[NLP模型-02-HMM](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-02-HMM/)
[NLP模型-03-GMM](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-03-GMM/)
[NLP模型-04-CRF](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-04-CRF/)
[NLP模型-05-transformer](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-05-transformer/)
[NLP模型-06-Bert](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-06-Bert/)
[NLP模型-07-LDA](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-07-LDA/)
[NLP模型-08-fastText](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-08-fastText/)
[NLP模型-09-Glove](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-09-Glove/)
[NLP模型-10-textRNN & textCNN](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-10-textRNN%20&%20textCNN/)
[NLP模型-11-seq2seq](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-11-seq2seq/)
[NLP模型-12-attention](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-12-attention/)
[NLP模型-13-XLnet](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-13-XLnet/)
[NLP模型-14-核方法](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-14-%E6%A0%B8%E6%96%B9%E6%B3%95/)
[NLP模型-15-深度玻尔兹曼机](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-15-%E6%B7%B1%E5%BA%A6%E7%8E%BB%E5%B0%94%E5%85%B9%E6%9B%BC%E6%9C%BA/)
[NLP模型-16-受限玻尔兹曼机](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-16-%E5%8F%97%E9%99%90%E7%8E%BB%E5%B0%94%E5%85%B9%E6%9B%BC%E6%9C%BA/)
[NLP模型-17-深度信念网络](https://iloveyou11.github.io/2020/07/28/NLP%E6%A8%A1%E5%9E%8B-17-%E6%B7%B1%E5%BA%A6%E4%BF%A1%E5%BF%B5%E7%BD%91%E7%BB%9C/)

受限玻尔兹曼机的全名为Restricted Boltzmann Machines（RBM），是一类具有两层结构、对称连接且无自反馈的随机神经网络模型，层间全连接，层内无连接。

以下图中，左边为玻尔兹曼机的结构，右边为受限玻尔兹曼机的结构：

<img src="https://i.loli.net/2020/07/29/nWK7rfZRzETbICx.png" alt="玻尔兹曼机结构图比较" width="80%" />

### RBM基本模型

<img src="https://i.loli.net/2020/07/29/FTnJv8ESALpWd1k.png" alt="RBM基本结构" width="80%" />

 RBM是一个无向图模型，`v`为可见层表示观测数据，`h`为隐层表示特征提取，`w`为两层之间的连接权重。BM中的隐单元和可见单元可以为任意的指数族单元（如softmax单元、高斯单元、泊松单元等）。常用的RBM一般是二值的，即不管是隐藏层还是可见层，它们的神经元的取值只为0或者1。

 RBM模型结构的结构：主要是权重矩阵`w`, 偏倚系数向量`a`和`b`，隐藏层神经元状态向量`h`和可见层神经元状态向量`v`。

 **RBM损失函数：**

 虽然说梯度下降从理论上可以用来优化RBM模型，但实际中是很难求得`P(v)`的概率分布的（`P(v)`表示可见层节点的联合概率）。计算复杂度非常大，因此采用一些随机采样的方法来得到近似的解。看这三个梯度的第二项实际上都是求期望，而我们知道，样本的均值是随机变量期望的无偏估计。因此一般都是基于对比散度方法来求解。

**RBM训练算法：**

对比散度算法（CD）

 <img src="https://i.loli.net/2020/07/29/Ki9XE8lzwShRM1m.png" alt="RBM训练算法" width="80%" />
