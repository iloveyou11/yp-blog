---
title: NLP模型-13-XLNet
date: 2020-07-19
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

### XLNet初识
XLNet 是一个类似 BERT 的模型，而不是完全不同的模型。`先将句子打乱，再复原句子。`XLNet融合了GPT和Bert两个模型，等效为mask机制+序列预测的难度加强版。

XLNet在2019年6月发布，在20个任务上超过了BERT的表现，并在18个任务上取得了SOA，包括机器问答、自然语言推断、情感分析和文档排序。

1. GPT是自编码模型， 通过双向LSTM编码提取语义。
2. Bert是自回归模型，不分顺序，用attention加强提取语义的效果。
3. XLNet融合了Bert的结构和GPT的有向预测。

<img src="https://i.loli.net/2020/07/16/wJriGV7cQYLtfNq.png" alt="XLNet" width="80%" />

XLNet解决了Bert的以下缺点：
1. 第一个预训练阶段因为采取引入[Mask]标记来Mask掉部分单词的训练模式，而Fine-tuning阶段是看不到这种被强行加入的Mask标记的，所以两个阶段存在使用模式不一致的情形，这可能会带来一定的性能损失
2. Bert在第一个预训练阶段，假设句子中多个单词被Mask掉，这些被Mask掉的单词之间没有任何关系，是条件独立的，而有时候这些单词之间是有关系的

<img src="https://i.loli.net/2020/07/17/iWoAPZ1TOuDqhnk.png" alt="XLNet改进" width="80%" />

详细模型解释见[《XLNet》](http://mantchs.com/2019/09/30/NLP/XLNet/)