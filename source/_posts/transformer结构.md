---
title: transformer
date: 2020-07-14
categories: AI
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

### 什么是transformer
一句话解释：`transformer`是带有`self-attention`的`seq2seq`，输出能同时计算，可以代替RNN结构（必须按输入的顺序计算），因此transformer对于sequence to sequence的应用场景更为高效。

**Transformer中抛弃了传统的CNN和RNN，整个网络结构完全是由Attention机制组成。**

<!-- more -->

### transformer结构解读
transformer的模型结构图如下：

<img src="https://i.loli.net/2020/07/12/SdtenNQgPkjm6iJ.jpg" width="40%" alt="transformer计算流程1" />

上图中，左半部分是Encoder，右半部分是Decoder。

1. Encoder
- `input`会通过`input embedding layer`变成一个`vector`
- 这个`vector`会加上`positional encoding`
- `positional encoding`接下来会进入灰色的block（灰色block会重复N次）
- 其中，灰色block的第一层是`Muti-head Attention`，sequence通过此层会得到另一个sequence
- 接下来通过`Add & Norm`，作用是将`Muti-Head Attention`的`output`和`Muti-head Attention`的`input`加起来再做layer normalization（和batch normalization不同）
- 接下来通过一个`Feed Forward layer`，会将sequence的每一个vector进行处理
- 然后再接上另一个`Add & Norm`

2. Decoder
- `outputs`会通过`output embedding layer`变成一个`vector`
- 这个`vector`会加上`positional encoding`
- `positional encoding`接下来会进入灰色的block（灰色block会重复N次）
- 其中，灰色block的第一层是`Masked Muti-head Attention`（masked：attend on the generated sequence），sequence通过此层会得到另一个sequence
- 接下来通过`Add & Norm`
- 再进入`Muti-Head Attention layer`接到之前`Encoder`部分的输出
- 接下来通过一个`Add & Norm`、`Feed Forward layer`、`Add & Norm`
- 再经过`Linear`、`Softmax`得到注重的输出。

**计算的具体流程如下图：**

第1步：根据Wq、Wk、Wv，计算每个vector对应的q、k、v，再根据右上角的公式计算αi

【PS】q、k、v分别代表Query向量（要去查询的，与每个词之间的关系），Key向量（等着被查的词）和Value向量（实际的特征信息），它们是通过3个不同的权值矩阵由嵌入向量X乘以三个不同的权值矩阵Wq、Wk、Wv得到

<img src="https://i.loli.net/2020/07/12/2N6PSW1F5ORb4zD.png" width="50%" alt="transformer计算流程1" />

第2步：对每个αi计算softmax值

<img src="https://i.loli.net/2020/07/12/LvVNyuGleXH84ba.png" width="50%" alt="transformer计算流程2" />

第3步：使用右上角的公式，计算每个vector对应的输出b

<img src="https://i.loli.net/2020/07/12/NLS2CcavgdDGwUA.png" width="50%" alt="transformer计算流程3" />

【注意】以上从x1到xn的运算是可以同时进行的，通过attention值的大小，可以与其他的vector产生上下文关联。

### 概念详解

**multi-headed机制**（通常都是多层，一层的不够的）

多组Q、K、V能得到多组表达（类似CNN中的filter），总综合多种特征提取方案，得到综合的特征表达
- 通过不同的head得到多个特征表达（一般是8个）
- 将所有特征拼接在一起
- 可以通过再一层全连接来降维（乘上全连接矩阵）

<img src="https://i.loli.net/2020/07/13/FnO2htRbZYKJcux.png" width="90%" alt="multi-headed机制" />

**位置信息如何表达？**

加入了位置信息的编码，每个词的位置发生变化，那就不同了。位置信息编码的方法非常多，常见的是加上一个周期信号。

**什么是layer normalization？**

<img src="https://i.loli.net/2020/07/13/Wyf6jEcHhu5I9dQ.png" width="90%" alt="layer norm" />

LN 是在每一个样本上计算均值和方差，而不是BN那种在批方向计算均值和方差。

Add & Norm做了残差连接（多条路的选择给模型自己选择）

**decoder的特点**

- attention计算方式不同（不只有self-attention，还多了encoder-decoder attention）
- 加入了mask机制（decoder的输出只能一个个输出，需要依赖前面的vector，因此需要使用mask机制，就是不能预知未来输出的东西）

### transformer为何计算速度快
我们可以将以上的计算过程使用一连串的矩阵乘法解决，而矩阵乘法可以轻易地使用GPU来加速。如下图所示：

**1. 每个vector的q、k、v的计算都可以使用矩阵相乘Wq*a、Wk*a、Wv*a加以计算：**

<img src="https://i.loli.net/2020/07/12/cV5HT1NZDBjloUf.png" width="50%" alt="transformer矩阵计算1" />

**2. α的计算也可以通过矩阵相乘表示不，如下图所示：**

（图一）

<img src="https://i.loli.net/2020/07/12/HkTUR4qDAmXW3C8.png" width="50%" alt="transformer矩阵计算2" />

（图二）

<img src="https://i.loli.net/2020/07/12/KWYHyGko3eEFTmB.png" width="50%" alt="transformer矩阵计算3" />

（图三）

<img src="https://i.loli.net/2020/07/12/ryFezKHq2cv5TJl.png" width="50%" alt="transformer矩阵计算4" />

**3. b的计算也可以通过矩阵相乘来解决：**

<img src="https://i.loli.net/2020/07/12/oM3ZXHFuBzD67Wn.png" width="50%" alt="transformer矩阵计算5" />

一下是综合矩阵运算的图解：

<img src="https://i.loli.net/2020/07/12/UzZWQCG64Et9y5P.png" width="50%" alt="transformer矩阵计算6" />

具体解析详见[NLP-06](https://iloveyou11.github.io/2020/04/05/NLP-06/)中的self-attention内容。

**attention整体计算流程：**
1. 每个词的Q会跟每个K计算得分（重要性越大，得分越高）
2. softmax后会得到整个加权结果（相当于归一化）
3. 此时每个词看的不只是他前面的序列，而是整个输入序列
4. 同一时间能计算出所有词的表达结果（利用矩阵乘法）

### Bert
Bert（Bidirectional Encoder Representations from Transformers）说白了就是`transformer`中的`encoder`部分，就是怎么将transformer模型中的特征学习出来，是Word2Vec的替代者。

1. 使用了Transformer作为算法的主要框架，能更彻底的捕捉语句中的双向关系；
2. 使用了Mask Language Model(MLM) 和 Next Sentence Prediction(NSP) 的多任务训练目标；
3. 使用更强大的机器训练更大规模的数据，使BERT的结果达到了全新的高度，并且Google开源了BERT模型，用户可以直接使用BERT作为Word2Vec的转换矩阵并高效的将其应用到自己的任务中。

#### 什么是预训练模型
假设我们有大量的维基百科数据，那么我们可以用这部分巨大的数据来训练一个泛化能力很强的模型，当我们需要在特定场景使用时，例如做文本相似度计算，那么，只需要简单的修改一些输出层，再用我们自己的数据进行一个增量训练，对权重进行一个轻微的调整。预训练的好处在于在特定场景使用时不需要用大量的语料来进行训练，节约时间效率高效，bert就是这样的一个泛化能力较强的预训练模型。

BERT预训练模型的输出结果，无非就是一个或多个向量。下游任务可以通过精调（改变预训练模型参数）或者特征抽取（不改变预训练模型参数，只是把预训练模型的输出作为特征输入到下游任务）两种方式进行使用。

#### 如何训练Bert
BERT是一个多任务模型，它的任务是由两个自监督任务组成，即MLM和NSP。

**任务1：Masked Language Model（MLM）**
在训练的时候随机从输入预料上mask掉一些单词（有15%的词汇被随机mask掉），然后通过的上下文预测该单词。交给模型自己去预测被mask的是什么，类似于完形填空。

**任务2：Next Sentence Prediction（NSP）**
预测两个句子是否应该连在一起，句子B是否是句子A的下文。训练数据的生成方式是从平行语料中随机抽取的连续两句话，其中50%保留抽取的两句话，它们符合IsNext关系，另外50%的第二句话是随机从预料中提取的，它们的关系是NotNext的。

<img src="https://i.loli.net/2020/07/13/4HJVnyOFmdNA9Eh.png" width="80%" alt="bert-句子判断" />

#### 举例说明
例如阅读理解题，输入文章和问题看，输出答案的位置。

如何设计网络呢？需要分别计算答案的起始位置和终止位置，如下图所示：

<img src="https://i.loli.net/2020/07/13/9wmjBbWXN5pTseY.png" width="80%" alt="bert-阅读" />

#### Bert模型如何使用

打开github/bert，下载pre-trained models，在此基础上做fine-tuning操作。

建议练手github/bert上列举的项目。
0. 中文项目`git clone https://github.com/ProHiryu/bert-chinese-ner`
1. download github/bert的代码，放入新建的项目文件夹（`git clone https://github.com/google-research/bert`）
2. 根据不同的任务，选择下载预训练模型，放入checkpoint文件夹
3. 下载数据集
4. 开始训练
5. 在output文件下面，可以找到eval_results.txt文件，就是训练的结果

#### 对于Bert的思考

> BERT适用场景
第一，如果NLP任务偏向在语言本身中就包含答案，而不特别依赖文本外的其它特征，往往应用Bert能够极大提升应用效果。典型的任务比如QA和阅读理解，正确答案更偏向对语言的理解程度，理解能力越强，解决得越好，不太依赖语言之外的一些判断因素，所以效果提升就特别明显。反过来说，对于某些任务，除了文本类特征外，其它特征也很关键，比如搜索的用户行为／链接分析／内容质量等也非常重要，所以Bert的优势可能就不太容易发挥出来。再比如，推荐系统也是类似的道理，Bert可能只能对于文本内容编码有帮助，其它的用户行为类特征，不太容易融入Bert中。
第二，Bert特别适合解决句子或者段落的匹配类任务。就是说，Bert特别适合用来解决判断句子关系类问题，这是相对单文本分类任务和序列标注等其它典型NLP任务来说的，很多实验结果表明了这一点。而其中的原因，我觉得很可能主要有两个，一个原因是：很可能是因为Bert在预训练阶段增加了Next Sentence Prediction任务，所以能够在预训练阶段学会一些句间关系的知识，而如果下游任务正好涉及到句间关系判断，就特别吻合Bert本身的长处，于是效果就特别明显。第二个可能的原因是：因为Self Attention机制自带句子A中单词和句子B中任意单词的Attention效果，而这种细粒度的匹配对于句子匹配类的任务尤其重要，所以Transformer的本质特性也决定了它特别适合解决这类任务。
从上面这个Bert的擅长处理句间关系类任务的特性，我们可以继续推理出以下观点：
既然预训练阶段增加了Next Sentence Prediction任务，就能对下游类似性质任务有较好促进作用，那么是否可以继续在预训练阶段加入其它的新的辅助任务？而这个辅助任务如果具备一定通用性，可能会对一类的下游任务效果有直接促进作用。这也是一个很有意思的探索方向，当然，这种方向因为要动Bert的第一个预训练阶段，所以属于NLP届土豪们的工作范畴，穷人们还是散退、旁观、鼓掌、叫好为妙。
第三，Bert的适用场景，与NLP任务对深层语义特征的需求程度有关。感觉越是需要深层语义特征的任务，越适合利用Bert来解决；而对有些NLP任务来说，浅层的特征即可解决问题，典型的浅层特征性任务比如分词，POS词性标注，NER，文本分类等任务，这种类型的任务，只需要较短的上下文，以及浅层的非语义的特征，貌似就可以较好地解决问题，所以Bert能够发挥作用的余地就不太大，有点杀鸡用牛刀，有力使不出来的感觉。
这很可能是因为Transformer层深比较深，所以可以逐层捕获不同层级不同深度的特征。于是，对于需要语义特征的问题和任务，Bert这种深度捕获各种特征的能力越容易发挥出来，而浅层的任务，比如分词／文本分类这种任务，也许传统方法就能解决得比较好，因为任务特性决定了，要解决好它，不太需要深层特征。
第四，Bert比较适合解决输入长度不太长的NLP任务，而输入比较长的任务，典型的比如文档级别的任务，Bert解决起来可能就不太好。主要原因在于：Transformer的self attention机制因为要对任意两个单词做attention计算，所以时间复杂度是n平方，n是输入的长度。如果输入长度比较长，Transformer的训练和推理速度掉得比较厉害，于是，这点约束了Bert的输入长度不能太长。所以对于输入长一些的文档级别的任务，Bert就不容易解决好。结论是：Bert更适合解决句子级别或者段落级别的NLP任务。          出自[《一文读懂BERT(原理篇)》](https://blog.csdn.net/jiaowoshouzi/java/article/details/89073944)