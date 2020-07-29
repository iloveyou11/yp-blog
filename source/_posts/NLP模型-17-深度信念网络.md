---
title: NLP模型-17-深度信念网络
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

2006年，Hinton等人提出了一种深度信念网络(Deep BeliefNets,DBN)，并给出了该模型的一个高效学习算法，成为了其后至今深度学习算法的主要框架。在该算法中，一个DBN模型`被视为由若干个RBM堆叠在一起`：训练时可通过从低到高逐层训练这些RBM来实现。

深度信念网络既可以用于非监督学习，类似于一个自编码机；也可以用于监督学习，作为分类器来使用。DBN由若干层神经元构成，组成元件是受限玻尔兹曼机（RBM）。

将若干个RBM“串联”起来则构成了一个DBN，其中，上一个RBM的隐层即为下一个RBM的显层，上一个RBM的输出即为下一个RBM的输入。训练过程中，需要充分训练上一层的RBM后才能训练当前层的RBM，直至最后一层。

<img src="https://i.loli.net/2020/07/29/2xcQ47lTqS5aN16.png" alt="DBN结构" width="80%" />

DBN 在训练模型的过程中主要分为两步:
1. 分别单独无监督地训练每一层 RBM 网络,确保特征向量映射到不同特征空间时,都尽可能多地保留特征信息（预训练）
2. 在 DBN 的最后一层设置 BP 网络,接收 RBM 的输出特征向量作为它的输入特征向量,有监督地训练实体关系分类器（微调）