---
title: word2vec及其扩展模型
date: 2020-07-12
categories: AI
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

<!-- more -->

### Word2Vec核心
将词转化为向量，在空间上的分布表示了其含义。两个词的含义越接近，词向量在空间上的距离也越近。（训练出的词向量有实际含义）

不管是 SG(skip-gram) 还是 CBOW，本质上，就是希望能够利用文章的上下文信息学习到连续空间的词表达，这是 Word2Vec 所有模型的核心。

**word2vec和word embedding的区别：**
word embedding 又包括很多不同的算法集合，或者叫做实现工具，比如 SEENA、FastText、word2vec 等。具体的层次关系为Representation → Distributed Reprensentation → word embedding → word2vec。

词向量模型：

<img src="https://i.loli.net/2020/07/15/KFjOm8HULMqCdJw.png" alt="词向量模型" width="100%" />

下面依次介绍各类词向量模型:

#### one-hot
one-hot编码，又称“独热编码”。每个状态都有独立的位置，且这些数字中只有一位有效，就是只能有一个状态。

one-hot编码具备以下缺点：
- 词与词之间相互独立，在实际情况中词与词应该是相互关联的
- 得到的特征是离散的，稀疏的
- 没有考虑词与词之间的顺序问题

1. **Boolean representation**
如下面所示，出现在词表中的那个位置，则对应位置为1，其他均为0:
```
sample1 -> [0,1,1,0,0,0,1,0,0]
sample2 -> [1,0,0,1,0,0,0,1,0]
sample3 -> [0,1,0,0,1,0,0,1,0]
sample4 -> [1,0,0,0,0,1,0,0,1]
```
2. **count representation**
如下面所示，统计了单词出现的个数，未出现则为0:
```
sample1 -> [0,2,1,3,0,0,2,0,0]
sample2 -> [1,0,0,1,0,2,0,1,0]
sample3 -> [0,1,3,0,1,1,0,1,0]
sample4 -> [1,0,4,0,0,3,0,0,1]
```
3. **tf-idf representation**
考虑了单词在语料库中的频率（重要性）

<img src="https://i.loli.net/2020/07/15/8Gkp6KCn93bwhyF.png" alt="tf-idf" width="60%" />

#### Skip-Gram
Skip-Gram：通过中间单词预测左右两侧的单词

<img src="https://i.loli.net/2020/07/15/i7fxzQqRgHBjcdv.png" alt="Skip-Gram" width="30%" />

#### CBOW
CBow：通过两边的单词预测中间的单词

<img src="https://i.loli.net/2020/07/15/1K7UB56wpfgjdPv.png" alt="CBOW" width="40%" />

#### Glove

<img src="https://i.loli.net/2020/07/15/XdlEa7IGKW6zZvj.png" alt="Glove" width="80%" />

#### Elmo（深度学习模型）

#### Bert

### Word2Vec扩展
1. 挖掘文档一级的隐含信息，学习到比词更加高一层级单元的隐含向量
2. 把 Word2Vec 的思想扩展到了另外一种离散数据图（Graph）的表达上。参考《线：大规模信息网络嵌入》（LINE: Large-scale Information Network Embedding）
3. 尝试在查询关键词和用户点击的网页之间建立上下文关系，使得 Word2Vec 模型可以学习到查询关键词以及网页的隐含向量。参考《用于支持搜索中查询重写的上下文和内容感知嵌入》（Context- and Content-aware Embeddings for Query Rewriting in Sponsored Search）