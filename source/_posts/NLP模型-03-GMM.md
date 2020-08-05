---
title: NLP模型-03-GMM
date: 2020-07-15
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

GMM是高斯混合模型（Gaussian Mixture Model）的简称。GMM模型是一种普遍使用的聚类算法，使用了高斯分布作为模型参数，EM算法进行训练。

### 高斯分布
高斯分布也叫正态分布，是一种在自然界大量的存在的、最为常见的分布形式。高斯分布的概率密度函数公式如下：

<img src="https://i.loli.net/2020/07/28/3NosgC6wS1nizJH.png" alt="正态分布公式" width="30%" />

参数`μ`表示均值，参数`σ`表示标准差，`σ`越小则数据越集中，`σ`越大则数据越分散。

```
这里做个小补充，数学中常见的分布：
- 正态分布：代表了宇宙中大多数情况的运转状态，大量的随机变量被证明是正态分布。
- 二项式分布：每次试验独立，每次试验只有两种结果（成功、失败）且概率相同，总共进行了n次试验。
- 泊松分布：
    泊松分布的条件如下：
    1）任何一个成功的事件都不影响另一个成功的事件
    2）在短时间内成功的概率必须等于在更长时间内成功的概率
    3）在时间间隔很小时，在给定时间间隔中成功的概率趋向于零
- 均匀分布：每时每刻发生的概率都是相同的
- 卡方分布：
    卡方分布：通过少数量的样本容量去预估总体容量的分布情况
    卡方检验：统计样本的实际观测值与理论推断值之间的偏离程度
- beta分布：beta分布可以看做是一个概率的概率分布，当你不知道一个东西的具体概率是多少时，它可以给出所有概率出现的概可能性大小。
```

### 期望最大与高斯训练
用最直观的文字讲一下EM训练过程：
1. 通过观察采样的概率值和模型概率值的接近程度，来判断一个模型是否拟合良好。
2. 通过调整模型以让新模型更适配采样的概率值。
3. 反复迭代多次，直到两个概率值非常接近时，停止训练即可

【核心】通过更新参数`μ`和`σ`来让期望值最大化。该过程和k-means的算法训练过程很相似（k-means不断更新类中心来让结果最大化），只不过在这里的高斯模型中，我们需要同时更新两个参数：`μ`和`σ`。

### 高斯混合模型
GMM是通过多个高斯分布的叠加来刻画数据的分布特征。GMM的公式如下：

<img src="https://i.loli.net/2020/07/28/fYFq56wDdbWPI3p.png" alt="GMM" width="30%" />

分布概率是K个高斯分布的和，每个高斯分布有属于自己的`μ`和`σ`参数，以及对应的权重参数，所有权重的和必须等于1。

举个例子，如下图所示：

<img src="https://i.loli.net/2020/07/28/j1m6lHBx3ouZiYe.png" alt="GMM身高例子" width="70%" />

女生的平均身高要比男生矮，如果已知性别，是可以很方便地求解出身高的概率值的。但是如果我们事先并不知道具体的性别，则不仅要学出每种分布的参数，还需要生成性别的划分情况，当决定期望值时，需要将权重值分别生成男性和女性的相应身高概率值并相加。