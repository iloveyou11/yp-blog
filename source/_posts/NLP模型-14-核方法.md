---
title: NLP模型-14-核方法
date: 2020-07-22
categories: NLP
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

### 核方法(kernal method)
核方法是把低维空间的非线性可分问题，转化为高维空间的线性可分问题的方法。当然,核方法不仅仅适用于SVM，还适用于其他数据为非线性可分的场景。对于非线性可分的训练集，可以大概率通过将其非线性映射到一个高维空间来转化成线性可分的训练集。
### 核技巧(kernal trick)
核技巧是一种利用核函数直接计算`φ(x),φ(z)`，以避开分别计算`φ(x)`和`φ(z)`，从而加速核方法计算的技巧。得益于SVM对偶问题的表现形式，核技巧可以应用于SVM。
### 核函数(kernal function)
核函数就是低维空间中的内积的某个函数，即核函数就等于就是高维空间的内积。

常用的核函数如下：

<img src="https://i.loli.net/2020/07/29/dEgFYhmB1cz6eUH.png" alt="核函数" width="60%" />

推荐观看大神的视频[《机器学习-白板推导系列-核方法》](https://www.bilibili.com/video/av34731384/?p=1)
推荐阅读大神的github笔记[Machine-Learning-Session](https://github.com/shuhuai007/Machine-Learning-Session)