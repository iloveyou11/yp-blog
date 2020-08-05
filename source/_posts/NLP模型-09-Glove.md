---
title: NLP模型-09-Glove
date: 2020-07-17
categories: AI
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

[NLP模型-01-预训练语言模型](https://iloveyou11.github.io/2020/07/14/NLP%E6%A8%A1%E5%9E%8B-01-%E9%A2%84%E8%AE%AD%E7%BB%83%E8%AF%AD%E8%A8%80%E6%A8%A1%E5%9E%8B/)
[NLP模型-02-HMM](https://iloveyou11.github.io/2020/07/14/NLP%E6%A8%A1%E5%9E%8B-02-HMM/)
[NLP模型-03-GMM](https://iloveyou11.github.io/2020/07/15/NLP%E6%A8%A1%E5%9E%8B-03-GMM/)
[NLP模型-04-CRF](https://iloveyou11.github.io/2020/07/15/NLP%E6%A8%A1%E5%9E%8B-04-CRF/)
[NLP模型-05-transformer](https://iloveyou11.github.io/2020/07/16/NLP%E6%A8%A1%E5%9E%8B-05-transformer/)
[NLP模型-06-Bert](https://iloveyou11.github.io/2020/07/16/NLP%E6%A8%A1%E5%9E%8B-06-Bert/)
[NLP模型-07-LDA](https://iloveyou11.github.io/2020/07/17/NLP%E6%A8%A1%E5%9E%8B-07-LDA/)
[NLP模型-08-fastText](https://iloveyou11.github.io/2020/07/17/NLP%E6%A8%A1%E5%9E%8B-08-fastText/)
[NLP模型-09-Glove](https://iloveyou11.github.io/2020/07/17/NLP%E6%A8%A1%E5%9E%8B-09-Glove/)
[NLP模型-10-textRNN & textCNN](https://iloveyou11.github.io/2020/07/18/NLP%E6%A8%A1%E5%9E%8B-10-textRNN%20&%20textCNN/)
[NLP模型-11-seq2seq](https://iloveyou11.github.io/2020/07/18/NLP%E6%A8%A1%E5%9E%8B-11-seq2seq/)
[NLP模型-12-attention](https://iloveyou11.github.io/2020/07/18/NLP%E6%A8%A1%E5%9E%8B-12-attention/)
[NLP模型-13-XLnet](https://iloveyou11.github.io/2020/07/19/NLP%E6%A8%A1%E5%9E%8B-13-XLnet/)
[NLP模型-14-核方法](https://iloveyou11.github.io/2020/07/22/NLP%E6%A8%A1%E5%9E%8B-14-%E6%A0%B8%E6%96%B9%E6%B3%95/)

GloVe是`Global Vectors for Word Representation`的简称，是一个基于全局词频统计的词表征工具，可以将单词表示为向量，这些向量可以刻画单词的语义特征，通过向量之间的距离可以计算两个单词之间的予以相似度。

#### Glove流程
1. 构建共现矩阵（就是文档中单词两两同时出现次数矩阵）

<img src="https://i.loli.net/2020/07/28/NxdB6MnUDhHlsik.png" alt="共现矩阵" width="50%" />

根据语料库构建一个共现矩阵X，矩阵中的每一个元素`Xij`代表单词`i`和上下文单词`j`在特定大小的上下文窗口内共同出现的次数。一般而言，这个次数的最小单位是1，但是GloVe不这么认为：它根据两个单词在上下文窗口的距离`d`，提出了一个衰减函数`decay=1/d` 用于计算权重，也就是说距离越远的两个单词所占总计数的权重越小。

2. 计算词向量和共现矩阵的近似关系
3. 构造损失函数
4. 训练GloVe模型

采用了AdaGrad的梯度下降算法，对矩阵 X 中的所有非零元素进行随机采样，学习曲率（learning rate）设为0.05，在vector size小于300的情况下迭代了50次，其他大小的vectors上迭代了100次，直至收敛。

#### 与其他词向量模型的区别

CBOW、SG（skip-gram）模型是一个`local context window`的方法，缺乏了整体的词和词的关系，负样本采用sample的方式会缺失词的关系信息。另外，直接训练SG类型的算法，很容易使得高曝光词汇得到过多的权重。
Glove融合了矩阵分解LSA的全局统计信息和`local context window`优势。融入全局的先验统计信息，可以加快模型的训练速度，又可以控制词的相对权重。
SG、CBOW每次都是用一个窗口中的信息更新出词向量，但是Glove则是用了全局的信息（共线矩阵），也就是多个窗口进行更新。

---

关于Glove模型的详细内容参考[《【NLP】GloVe》](http://mantchs.com/2019/08/24/NLP/GloVe/)