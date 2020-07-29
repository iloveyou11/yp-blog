---
title: NLP模型-15-深度玻尔兹曼机
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

波尔兹曼机是1986年提出的一种根植于统计力学的随机神经网络。这种网络中的神经元是随机神经元，神经元的输出只有两种状态（未激活、激活）。BM是由随机神经元全连接组成的反馈神经网络，且对称连接，无自反馈：包含一个可见层和一个隐层。

BM具有强大的无监督学习能力，能够学习数据中复杂的规则。但是，拥有这种学习能力的代价是其训练（学习）时间非常长。但是，我们无法准确地计算BM所表示的分布，也很难得到服从BM所表示分布的随机样本。为了克服这个问题，后来引入了限制的波尔兹曼机(Restricted Boltzman Machine , RBM)，RBM具有一个可见层，一个隐层，层内无连接。

<img src="https://i.loli.net/2020/07/29/nWK7rfZRzETbICx.png" alt="玻尔兹曼机结构图比较" width="80%" />

加深RBM的层数后，就变成了DBM（深度玻尔兹曼机），结构图如下：

<img src="https://i.loli.net/2020/07/29/UAyvSprifc2kV1o.png" alt="DBM" width="80%" />
