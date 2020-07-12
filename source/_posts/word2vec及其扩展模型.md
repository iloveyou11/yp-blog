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
不管是 SG(skip-gram) 还是 CBOW，本质上，就是希望能够利用文章的上下文信息学习到连续空间的词表达，这是 Word2Vec 所有模型的核心。

### Word2Vec扩展
1. 挖掘文档一级的隐含信息，学习到比词更加高一层级单元的隐含向量
2. 把 Word2Vec 的思想扩展到了另外一种离散数据图（Graph）的表达上。参考《线：大规模信息网络嵌入》（LINE: Large-scale Information Network Embedding）
3. 尝试在查询关键词和用户点击的网页之间建立上下文关系，使得 Word2Vec 模型可以学习到查询关键词以及网页的隐含向量。参考《用于支持搜索中查询重写的上下文和内容感知嵌入》（Context- and Content-aware Embeddings for Query Rewriting in Sponsored Search）