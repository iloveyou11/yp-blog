---
title: NLP系列2：分词与文本表示
date: 2020-03-20
categories: AI
author: yangpei
tags:
  - 自然语言处理
comments: true
cover_picture: /images/banner.jpg
---

核心内容：中文分词、拼写错误纠正、停用词过滤、stemming操作、文本表示、文本相似度计算、倒排表、语言模型LM、masked LM、平滑算法

<!-- more -->

[NLP系列1：NLP简介](https://iloveyou11.github.io/2020/03/19/NLP-01/)
[NLP系列2：分词与文本表示](https://iloveyou11.github.io/2020/03/20/NLP-02/)
[NLP系列3：语言系统与NLP基础](https://iloveyou11.github.io/2020/03/21/NLP-03/)
[NLP系列4：NLP核心任务](https://iloveyou11.github.io/2020/03/22/NLP-04/)
[NLP系列5：重要模型与算法](https://iloveyou11.github.io/2020/03/23/NLP-05/)
[NLP系列6：词向量与文本生成](https://iloveyou11.github.io/2020/03/24/NLP-06/)

#### 分词
1. 最大匹配算法（贪心算法）
包括前向最大匹配和后向最大匹配，缺点是不能考虑语义，可能是局部最优
2. 考虑语义
基本思想：根据输入的句子，生成所有可能的分割，根据LM（语言模型，用来评估句子的合理程度，后面会讲到）选择其中最好的分割方式，但是效率低下。
3. 维特比算法
**如何解决效率问题？**
——根据词典概率，构建连接图，使用DP动态规划

<img width="70%" src="https://i.loli.net/2020/03/24/wEi7GCpbOzsnx38.jpg" alt="维特比算法" />

核心问题变为：如何找到最短的路径

**总结：**
1. 基于匹配规则的方法（max-matching）
2. 基于概率的方法（LM,HMM,CRF）
3. 分词可以认为是已经解决的问题，基本不会涉及到重新设计分词，可能会根据业务需求有小的修改

#### 拼写错误纠正
1. 计算出与用户输入单词编辑距离为1、2的所有单词，过滤掉无用单词；
2. 使用贝叶斯算法计算p，`p(s|c)=p(c|s)*p(c)/p(s)`，p(s)是定值，只需求出`p(c|s)*p(c)`的最大值即可

<img width="50%" src="https://i.loli.net/2020/03/24/iHqoPMawrx7Ehd8.jpg" alt="拼写错误纠正1" />

**为什么选择编辑距离为1、2的字符串？**
因为这样可以覆盖99%以上的场景。
**如何选择？**

<img width="60%" src="https://i.loli.net/2020/03/24/6wJX5nP7zlx9MoL.jpg" alt="拼写错误纠正2" />

**最小编辑距离算法是怎么样的？**
最小编辑距离是指一个单词通过新增、替换、删除三种操作方法，变为另一个单词的最小操作数。使用DP动态规划解决，算法如下：

<img width="60%" src="https://i.loli.net/2020/03/24/YErL8jTKQIHk95w.jpg" alt="最小编辑距离" />

#### 停用词过滤、stemming
停用词过滤：去掉停用词——出现频率很低、不重要的词汇
stemming：标准化操作，将不同状态的词转化为统一状态，如：
```
went,go,going->go
fly,flies->fli
fast,faster,fastest->fast
```
Porter Stemmer:

<img width="60%" src="https://i.loli.net/2020/03/24/A2sqw457hxynt1K.jpg" alt="stemmer" />

Stemmer的操作要依赖于语言学家的经验

#### 文本表示
1. **one-hot表示法**
one-hot有以下三种形式
- boolean-based representation（考虑是否出现，出现是1，否则是0）
- count-based representation（考虑单词的计数）
- tfidf-based representation（考虑了单词重要性，不是出现次数越多代表越重要！不是出现次数越少代表越不重要！）
**tf-idf**的计算公式为： `tfidf(w)=tf(d,w)*idf(w)`，其中`tf(d,w)`表示文档d中w单词出现的词频，`idf(w)`表示单词的重要性，`idf(w)=log(N/N(w))`

2. **词向量表示法**（考虑了单词的语义）
使用词向量word vector表示单词，称为分布式表示法。常见的深度学习模型有skip-gram、glove、CBow、RNN/LSTM、MF（矩阵分解）……

<img width="60%" src="https://i.loli.net/2020/03/24/1nK8ShFtClQ9mE3.png" alt="词向量可视化" />

使用深度学习训练词向量模型，很多大公司利用丰富的语料库已经训练好了词向量，我们需要知道有哪些词向量模型，并加以利用。但是如果我们像用于特定的使用场景，如金融、医疗、法律等等，我们需要特殊的语料库并自己训练模型。
词向量可以代表单词的含义。

#### 文本相似度计算
1. 欧氏距离
2. 余弦相似度

#### 倒排表
在问答系统中，根据用户输入在知识库中匹配问题，找到相似度最大的问题。如果知识库有n个问题，则需要计算n次。复杂度为`o(n)*o(相似度计算)`，复杂度太高，不能满足实时性的要求。

<img width="60%" src="https://i.loli.net/2020/03/24/UOtBQIfdwMDNJnq.jpg" alt="倒排表" />

引入倒排表来提高系统的时间效率，核心思路是“层次过滤思想”。遇到输入问题时，做一层层的过滤，先抛掉毫不沾边的样本，再依次过滤……而不是一个个地计算余弦相似度。

#### 语言模型LM
是否一句话从语法上通顺
unigram
`p(w1,w2,w3,w4,w5)=p(w1)+p(w2)+p(w3)+p(w4)+p(w5)`
bigram:
`p(w1,w2,w3,w4,w5)= p(w1)+p(w2|w1)+p(w3|w2)+p(w4|w3)+p(w5|w4)`
trigram:
`p(w1,w2,w3,w4,w5)= p(w1)+p(w2|w1)+p(w3|w1w2)+p(w4|w2w3)+p(w5|w3w4)`
依此类推可得n-gram

N-Gram语言模型的评价方法：
1. 实用方法
通过查看该模型在实际应用中（如拼写检查、机器翻译）中的标显来评价，非常直观实用，但是缺乏针对性、不够客观
2. 理论方法
perplexity（迷惑度）计算，基本思想是给测试集赋予较高概率值的语言模型较好

评价LM的质量（计算perplexity）：
`perplexity=2^(-x)   x是最大似然估计`

<img width="70%" src="https://i.loli.net/2020/03/24/rkECxu3j9W6UPKO.jpg" alt="perplexity" />

例如，训练的三个模型unigram、bigram、trigram，perplexity分别为300、200、100，可知此次训练的trigram模型最好。

#### masked LM
把部分单词“挖掉”，再来做预测（增加噪声，提高模型稳定性）

#### 平滑算法
解决的问题：有些单词会未存在语料库中（新单词），如何评判它的重要程度？
1. add-one smoothing
2. add-k smoothing
3. interpolation
4. good-turning smoothing（考虑了新的单词）
算法具体计算方法如下：

<img width="100%" src="https://i.loli.net/2020/03/24/9EfpiKge6r3mxZF.jpg" alt="平滑算法" />

其中，`add-k smoothing`的分母加V是为了确保概率和为1，`interpolation`是使用unigram、bigram、trigram分别做加权平均，`good-turning smoothing`方法存在一个问题，就是Nc有数据，但Nc依赖于Nc+1，而Nc+1可能不存在（仅出现过c+1次的单词不存在），这时应该采用曲线拟合来解决。
**思考：**为什么这些算法会成立呢？