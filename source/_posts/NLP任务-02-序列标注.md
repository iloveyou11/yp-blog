---
title: NLP任务-02-序列标注
date: 2020-07-28
categories: AI
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

[NLP任务-01-词嵌入](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-01-%E8%AF%8D%E5%B5%8C%E5%85%A5/)
[NLP任务-02-序列标注](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-02-%E5%BA%8F%E5%88%97%E6%A0%87%E6%B3%A8/)
[NLP任务-03-分词](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-03-%E5%88%86%E8%AF%8D/)
[NLP任务-04-词性标注](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-04-%E8%AF%8D%E6%80%A7%E6%A0%87%E6%B3%A8/)
[NLP任务-05-命名实体识别](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-05-%E5%91%BD%E5%90%8D%E5%AE%9E%E4%BD%93%E8%AF%86%E5%88%ABNER/)
[NLP任务-06-依存句法分析](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-06-%E4%BE%9D%E5%AD%98%E5%8F%A5%E6%B3%95%E5%88%86%E6%9E%90/)
[NLP任务-07-信息抽取](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-07-%E4%BF%A1%E6%81%AF%E6%8A%BD%E5%8F%96/)
[NLP任务-08-文本分类](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-08-%E6%96%87%E6%9C%AC%E5%88%86%E7%B1%BB/)
[NLP任务-09-文本聚类](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-09-%E6%96%87%E6%9C%AC%E8%81%9A%E7%B1%BB/)
[NLP任务-10-自动补全](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-10-%E8%87%AA%E5%8A%A8%E8%A1%A5%E5%85%A8/)
[NLP任务-11-语义消歧](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-11-%E8%AF%AD%E4%B9%89%E6%B6%88%E6%AD%A7/)
[NLP任务-12-问答系统](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-12-%E9%97%AE%E7%AD%94%E7%B3%BB%E7%BB%9F/)
[NLP任务-13-机器翻译](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-13-%E6%9C%BA%E5%99%A8%E7%BF%BB%E8%AF%91/)
[NLP任务-14-自动摘要](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-14-%E8%87%AA%E5%8A%A8%E6%91%98%E8%A6%81/)
[NLP任务-15-情感分析](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-15-%E6%83%85%E6%84%9F%E5%88%86%E6%9E%90/)
[NLP任务-16-聊天机器人](https://iloveyou11.github.io/2020/07/28/NLP%E4%BB%BB%E5%8A%A1-16-%E8%81%8A%E5%A4%A9%E6%9C%BA%E5%99%A8%E4%BA%BA/)

序列标注包括了以下几个方面：
1. 中文分词——人们提出了{B,M,E,S}这种最流行的标注集，{B,M,E,S}分为代表{Begin,Middle,End,Single}
2. 词性标注——根据单词序列，标注出词性序列
3. 命名实体识别——命名实体识别可以复用{B,M,E,S}标注集，但是还需要确定实体所属的类别

`序列标注方面`，传统的统计方法是HMM(隐马尔可夫)、MEMM(最大熵马尔可夫)、CRF(条件随机场)，基本就和命名实体识别类似了，而在深度学习引入后，形成了输入层、编码层、解码层的主要架构，同过预训练表征模型(如w2v)、深度学习结构(CNN、RNN等)以及输出层(CRF、softmax)等结构链接，完成最基本的结构。

**数据标注方式：**
主要有BIO和BIOES两种。BIOES如下：
```
BIOES如下：

B，即Begin，表示开始
I，即Intermediate，表示中间
E，即End，表示结尾
S，即Single，表示单个字符
O，即Other，表示其他，用于标记无关字符

BIO如下：

B，即Begin，表示开始
I，即Intermediate，表示中间
E，即End，表示结尾
```

#### HMM与序列标注
**具体流程：**

<img src="https://i.loli.net/2020/07/23/MPQpLulqTESiIna.png" alt="HMM参数" width="80%" />

当获得了分好词的语料之后，三个概率`θ=(A,B,Π)`可以通过如下方式获得：
(1) 初始状态概率`Π`-`P(z1)`
统计每个句子开头，序列标记分别为B，S的个数，最后除以总句子的个数，即得到了初始概率矩阵。
(2) 状态转移概率`A`-`(zi|zi-1)`
根据语料，统计不同序列状态之间转化的个数，例如`count(yi=”E”|yi-1=”M”)`为语料中i-1时刻标为“M”时，i时刻标记为“E”出现的次数。得到一个`4*4`的矩阵，再将矩阵的每个元素除以语料中该标记字的个数，得到状态转移概率矩阵。
(3) 输出观测概率`B`-`P(xi|zi)`
根据语料，统计由某个隐藏状态输出为某个观测状态的个数，例如`count(xi=”深”|yi=”B”)`为i时刻标记为“B”时，i时刻观测到字为“深”的次数。得到一个`4*N`的矩阵，再将矩阵的每个元素除以语料中该标记的个数，得到输出观测概率矩阵。

训练结束后，即可获得三个概率矩阵`θ=(A,B,Π)`，接下来需要使用维特比算法获得一个句子的最大概率分词标记序列。

<img src="https://i.loli.net/2020/07/27/qPQNLlFvnzTyUHr.png" alt="NER-HMM" width="80%" />

```
第一个词为“我”，通过初始概率矩阵和输出观测概率矩阵分别计算delta1("B")=P(y1=”S”)P(x1=”我”|y1=”S”)，delta1("M")=P(y1=”B”)P(x1=”我”|y1=”B”)，delta1("E")=P(y1=”M”)P(x1=”我”|y1=”M”)，delta1("S")=P(y1=”E”)P(x1=”我”|y1=”E”)，并设kethe1("B")=kethe1("M")=kethe1("E")=kethe1("S")=0；
同理利用公式分别计算：
delta2("B")，delta2("M")，delta2("E")，delta2("S")。图中列出了delta2("S")的计算过程，就是计算：
P(y2=”S”|y1=”B”)P(x2=”爱”|y2=”S”)
P(y2=”S”|y1=”M”)P(x2=”爱”|y2=”S”)
P(y2=”S”|y1=”E”)P(x2=”爱”|y2=”S”)
P(y2=”S”|y1=”S”)P(x2=”爱”|y2=”S”)
其中P(y2=”S”|y1=”S”)P(x2=”爱”|y2=”S”)的值最大，为0.034，因此delta2("S")，kethe2("S")="S"，同理，可以计算出delta2("B")，delta2("M")，delta2("E")及kethe2("B")，kethe2("M")，kethe2("E")。

同理可以获得第三个和第四个序列标记的delta和kethe。
到最后一个序列，delta4("B")，delta4("M")，delta4("E")，delta4("S")中delta4("S")的值最大，因此，最后一个状态为”S”。
最后，回退，
i3 = kethe4("S") ="B"
i2 =kethe3("B") = "S"
i1 = kethe2("S") ="S"
求得序列标记为：“SSBE”。
```

**HMM解决序列标注问题的优势与不足：**
HMM时非常适合用于序列标注问题，但HMM引入了马尔科夫假设，即T时刻的状态仅仅与前一时刻的状态相关。但是，语言往往是前后文相互照应的，所以HMM可能会有它的局限和问题，我们可以思考一下，如何解决这个问题。

#### CRF与序列标注
NER任务特征提取的网路结构如下：

<img src="https://i.loli.net/2020/07/27/7sqMkygxn83UYJO.png" alt="NER-CRF" width="80%" />

句子经过双向LSTM进行特征提取之后，会得到一个特征输出。训练时，将这个特征和对应的label输入到条件随机场中，就可以计算损失了。预测时，将自然语言输入到该网络，经CRF就可以识别该句子中的实体了。

`条件随机场(CRF)在现今NLP中序列标记任务中是不可或缺的存在。太多的实现基于此，例如LSTM+CRF，CNN+CRF，BERT+CRF。因此，这是一个必须要深入理解和吃透的模型。！！`

#### LSTM+CRF与序列标注
采用LSTM作为特征抽取器，再接一个CRF层来作为输出层，结构如下图所示：

<img src="https://i.loli.net/2020/07/27/aBxdrj6Nos7SOWK.png" alt="NER-LSTM+CRF" width="80%" />

#### CNN+CRF与序列标注
采用LSTM作为特征抽取器，再接一个CRF层来作为输出层，结构如下图所示：

<img src="https://i.loli.net/2020/07/27/fu8t9FBAh6yQCHb.png" alt="NER-CNN+CRF" width="80%" />

虽然CNN并不太擅长长序列的特征提取，但是CNN具有非常高效的并行运算能力，能够加快运算速度。

#### BERT+（LSTM）+CRF与序列标注
利用预训练好的BERT模型，再用少量的标注数据进行fine tune，能够快速地实现NER任务。

<img src="https://i.loli.net/2020/07/27/MC2DtKFon9jUhPz.png" alt="NER-BERT+CRF" width="80%" />