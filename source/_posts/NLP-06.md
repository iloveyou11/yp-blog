---
title: NLP系列6：词向量与文本生成
date: 2020-03-24
categories: AI
author: yangpei
tags:
  - 自然语言处理
comments: true
cover_picture: /images/banner.jpg
---

核心内容：词向量、xgboost、RNN、LSTM、GRU、attention机制、self-attention、good-representation、seq2seq、看图说话、深度文本匹配、BERT、LDA、transform

<!-- more -->

[NLP系列1：NLP简介](https://iloveyou11.github.io/2020/03/19/NLP-01/)
[NLP系列2：分词与文本表示](https://iloveyou11.github.io/2020/03/20/NLP-02/)
[NLP系列3：语言系统与NLP基础](https://iloveyou11.github.io/2020/03/21/NLP-03/)
[NLP系列4：NLP核心任务](https://iloveyou11.github.io/2020/03/22/NLP-04/)
[NLP系列5：重要模型与算法](https://iloveyou11.github.io/2020/03/23/NLP-05/)
[NLP系列6：词向量与文本生成](https://iloveyou11.github.io/2020/03/24/NLP-06/)

#### 词向量（wordvector）

<img width="100%" src="https://i.loli.net/2020/03/25/lxXWp9yaeIjfq8Y.jpg" alt="词向量" />

在NLP领域中，为了能表示人类的语言符号，一般会把这些符号转成一种数学向量形式以方便处理，我们把语言单词嵌入到向量空间中就叫`词嵌入（word embedding）`。
比如有比较流行的谷歌开源的 word2vec ，它能生成词向量，通过该词向量在一定程度上还可以用来度量词与词之间的相似性。word2vec采用的模型包含了连续词袋模型（CBOW）和Skip-Gram模型，并通过神经网络来训练。

1. one-hot形式的词向量
one-hot形式的维数通常会很大，因为词数量一般在10W级别，这会导致训练时难度大大增加，造成维数灾难。另外这么多维只以顺序信息并且只用1和0来表示单词，很浪费空间。再一个是这种方式的任意两个词都是孤立的，没法看出两个词之间的相似性。
2. 分布式词向量
鉴于one-hot形式词向量的缺点，出现了另外一种词向量表示方式——分布式词向量(distributed word representation)。 分布式词向量则干脆直接用普通的向量来表示词向量，而元素的值为任意实数，该向量的维数可以在事前确定，一般可以为50维或100维。
##### CBOW（连续词袋模型）
该方法就是将{“The”，“cat”，“over”，“the”，“puddle”}当做一个上下文（或语义背景），然后根据这些单词，预测并产生中心单词“jumped”，这种模型我们称作为CBOW模型。
##### Skip-Gram
这种方法就是通过给出的中心词“jumped”来创建一个模型，来预测和产生周围词汇（或者中心词的上下文）“The”,“cat”,"over,“the”,“puddle”。这里我们称之为“跳跃”的上下文，将这种类型的模型称为Skip-Gram模型。
与CBOW相比，初始化时大部分是相同的，只是我们需要将x和y，就是在CBOW中的x现在是y，反之亦然。我将输入one hot向量记为x，输出向量记为y(c)，V、U和CBOW模型一样。
##### subword
##### ELMo
#### xgboost
- `决策树（Decision Tree）：`每个 HR 都有一系列标准，比如学历、工作年份、面试表现等。一个决策树就类似于一个 HR 基于他的这些标准来筛选候选人。
- `Bagging：`假设现在不只有一个面试官，而是有一个面试小组，组中每个面试官都有投票权。Bagging 和 Bootstrap 就是通过一个民主投票的过程，将所有面试官的输入聚合起来，得到一个最终的决定。
- `随机森林（Random Forest）：`它是一种基于 Bagging 的算法，关键点在于随机森林会随机使用特征的子集。换句话说，就是每个面试官都只会用一些随机选择的标准来考验候选人的任职资格（比如，技术面值考察编程技能，行为面只考察非技术相关的技能）。
- `Boosting：`这是一种替代方法，每个面试官都会根据上一个面试官的面试结果来改变自己的评价标准。通过利用更加动态的评估过程，可以提升（boost）面试过程的效率。
- `梯度提升（Gradient Boosting）：`Boosting 的特例，用梯度下降算法来将误差最小化。比如，咨询公司用案例面试来剔除不太合格的候选人。
- `XGBoost：`可以认为 XGBoost 就是“打了兴奋剂”的梯度提升（因此它全称是“Extreme Gradient Boosting” —— 极端梯度提升）。它是软件和硬件优化技术的完美结合，可以在最短的时间内用较少的计算资源得到出色的结果。

#### RNN
[循环神经网络（Recurrent Neural Network，RNN）](https://juejin.im/entry/5b97e36cf265da0aa81be239)
#### LSTM
[详解 LSTM](https://juejin.im/entry/58f1e59ba0bb9f006a93ea53)
#### GRU
[GRU神经网络](GRU神经网络)
#### attention机制
#### self-attention
#### good-representation
#### seq2seq
#### 看图说话
#### 深度文本匹配
#### BERT
#### 主题模型（LDA）
#### transform