---
title: NLP系列6：词向量与文本生成
date: 2020-03-24
categories: NLP
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

核心内容：词向量、xgboost、RNN、LSTM、GRU、attention机制、self-attention、good-representation、seq2seq、看图说话、深度文本匹配、BERT、LDA、transform

<!-- more -->


### 词向量（wordvector）

<img width="100%" src="https://i.loli.net/2020/03/25/lxXWp9yaeIjfq8Y.jpg" alt="词向量" />

在NLP领域中，为了能表示人类的语言符号，一般会把这些符号转成一种数学向量形式以方便处理，我们把语言单词嵌入到向量空间中就叫`词嵌入（word embedding）`。
比如有比较流行的谷歌开源的 word2vec ，它能生成词向量，通过该词向量在一定程度上还可以用来度量词与词之间的相似性。word2vec采用的模型包含了连续词袋模型（CBOW）和Skip-Gram模型，并通过神经网络来训练。

1. one-hot形式的词向量
one-hot形式的维数通常会很大，因为词数量一般在10W级别，这会导致训练时难度大大增加，造成维数灾难。另外这么多维只以顺序信息并且只用1和0来表示单词，很浪费空间。再一个是这种方式的任意两个词都是孤立的，没法看出两个词之间的相似性。
2. 分布式词向量
鉴于one-hot形式词向量的缺点，出现了另外一种词向量表示方式——分布式词向量(distributed word representation)。 分布式词向量则干脆直接用普通的向量来表示词向量，而元素的值为任意实数，该向量的维数可以在事前确定，一般可以为50维或100维。
#### CBow（连续词袋模型）
该方法就是将{“The”，“cat”，“over”，“the”，“puddle”}当做一个上下文（或语义背景），然后根据这些单词，预测并产生中心单词“jumped”，这种模型我们称作为CBow模型。
#### Skip-Gram
这种方法就是通过给出的中心词“jumped”来创建一个模型，来预测和产生周围词汇（或者中心词的上下文）“The”,“cat”,"over,“the”,“puddle”。这里我们称之为“跳跃”的上下文，将这种类型的模型称为Skip-Gram模型。
与CBOW相比，初始化时大部分是相同的，只是我们需要将x和y，就是在CBOW中的x现在是y，反之亦然。我将输入one hot向量记为x，输出向量记为y(c)，V、U和CBOW模型一样。
#### subword
解决痛点：低频词、OOV（新词不存在已有此库中）

Subword 背后的思想: 大多数现有方法都用一个分离的 vector 来表示一个 word, 这就忽视了 word 的内在结构, 而它可能蕴含丰富的词意. 这对于拉丁语系的语言可能是很有帮助的. 这样做的一个好处是, 对于 corpus 中出现频率极低的, 或者从未出现过的 word, 利用 character level information, 能更好地表示它们.
在一个 word 前后加上 < 与 >, 然后将单词分成 n-gram 的词袋, 此处前后缀能起到区别于其他单词序列的作用. 比如使用 3-gram, where 被表示成 <wh, whe, her, ere, re>. 英文单词 her 的词袋是 <he, her, er>, 因此不应将 where 中的 3-gram her 与单词 her 搞混. 另外, 将带前后缀的原单词 <where> 也加入词袋. 最后 word vector 就用 n-gram vectors 的和表示.
Subwords 模型允许在 word 间共享表示, 因此对于低频单词, 能学到更可靠的表示.

#### ELMo
`ELMO`是`Embedding from Language Models`的简称，其实这个名字并没有反应它的本质思想，提出ELMO的论文题目：“Deep contextualized word representation”更能体现其精髓，而精髓在哪里？在deep contextualized这个短语，一个是deep，一个是context，其中context更关键。
在此之前的Word Embedding本质上是个静态的方式，所谓静态指的是训练好之后每个单词的表达就固定住了，以后使用的时候，不论新句子上下文单词是什么，这个单词的Word Embedding不会跟着上下文场景的变化而改变，所以对于比如Bank这个词，它事先学好的Word Embedding中混合了几种语义 ，在应用中来了个新句子，即使从上下文中（比如句子包含money等词）明显可以看出它代表的是“银行”的含义，但是对应的Word Embedding内容也不会变，它还是混合了多种语义。这是为何说它是静态的，这也是问题所在。
**ELMO的本质思想是：**我事先用语言模型学好一个单词的Word Embedding，此时多义词无法区分，不过这没关系。在我实际使用Word Embedding的时候，单词已经具备了特定的上下文了，这个时候我可以根据上下文单词的语义去调整单词的Word Embedding表示，这样经过调整后的Word Embedding更能表达在这个上下文中的具体含义，自然也就解决了多义词的问题了。所以ELMO本身是个根据当前上下文对Word Embedding动态调整的思路。
ELMO采用了典型的两阶段过程，第一个阶段是利用语言模型进行预训练；第二个阶段是在做下游任务时，从预训练网络中提取对应单词的网络各层的Word Embedding作为新特征补充到下游任务中。

**ELMO有什么值得改进的缺点呢？**
- 首先，一个非常明显的缺点在特征抽取器选择方面，ELMO使用了LSTM而不是新贵Transformer，Transformer是谷歌在17年做机器翻译任务的“Attention is all you need”的论文中提出的，引起了相当大的反响，很多研究已经证明了Transformer提取特征的能力是要远强于LSTM的。如果ELMO采取Transformer作为特征提取器，那么估计Bert的反响远不如现在的这种火爆场面。
- 另外一点，ELMO采取双向拼接这种融合特征的能力可能比Bert一体化的融合特征方式弱，但是，这只是一种从道理推断产生的怀疑，目前并没有具体实验说明这一点。

### xgboost
[机器学习算法之XGBoost](https://juejin.im/entry/5c89afd8e51d4559d83387a6)
XGBoost相比于GBDT加入了正则化项(Regularization)
> 我们使用损失函数优化是为了避免欠拟合，而使用正则化项就是为了避免过拟合。正则化项与损失函数共同组成了我们的目标函数。XGBoost比GBDT多添加了以树复杂度构成的正则化项，也是XGBoost实际表现更为优秀的原因之一

XGBoost 所应用的算法就是 gradient boosting decision tree，既可以用于分类也可以用于回归问题中。那什么是 Gradient Boosting？Gradient boosting 是 boosting 的其中一种方法。所谓 Boosting ，就是将弱分离器 f_i(x) 组合起来形成强分类器 F(x) 的一种方法。

- `决策树（Decision Tree）：`每个 HR 都有一系列标准，比如学历、工作年份、面试表现等。一个决策树就类似于一个 HR 基于他的这些标准来筛选候选人。
- `Bagging：`假设现在不只有一个面试官，而是有一个面试小组，组中每个面试官都有投票权。Bagging 和 Bootstrap 就是通过一个民主投票的过程，将所有面试官的输入聚合起来，得到一个最终的决定。
- `随机森林（Random Forest）：`它是一种基于 Bagging 的算法，关键点在于随机森林会随机使用特征的子集。换句话说，就是每个面试官都只会用一些随机选择的标准来考验候选人的任职资格（比如，技术面值考察编程技能，行为面只考察非技术相关的技能）。
- `Boosting：`这是一种替代方法，每个面试官都会根据上一个面试官的面试结果来改变自己的评价标准。通过利用更加动态的评估过程，可以提升（boost）面试过程的效率。
- `梯度提升（Gradient Boosting）：`Boosting 的特例，用梯度下降算法来将误差最小化。比如，咨询公司用案例面试来剔除不太合格的候选人。
- `XGBoost：`可以认为 XGBoost 就是“打了兴奋剂”的梯度提升（因此它全称是“Extreme Gradient Boosting” —— 极端梯度提升）。它是软件和硬件优化技术的完美结合，可以在最短的时间内用较少的计算资源得到出色的结果。

### RNN
[循环神经网络（Recurrent Neural Network，RNN）](https://juejin.im/entry/5b97e36cf265da0aa81be239)
**为什么使用序列模型（sequence model）？**
标准的全连接神经网络（fully connected neural network）处理序列会有两个问题：1）全连接神经网络输入层和输出层长度固定，而不同序列的输入、输出可能有不同的长度，选择最大长度并对短序列进行填充（pad）不是一种很好的方式；2）全连接神经网络同一层的节点之间是无连接的，当需要用到序列之前时刻的信息时，全连接神经网络无法办到，一个序列的不同位置之间无法共享特征。而循环神经网络（Recurrent Neural Network，RNN）可以很好地解决问题。

优秀文章：
[RNN 循环神经网络系列 1：基本 RNN 与 CHAR-RNN](https://juejin.im/post/59f0c5b0f265da43085d3e94)
[RNN 循环神经网络系列 2：文本分类](https://juejin.im/post/59f0c6b3f265da4319557de4)
[RNN 循环神经网络系列 3：编码、解码器](https://juejin.im/post/59fc1616f265da432b4a2d44)
[RNN 循环神经网络系列 4：注意力机制](https://github.com/xitu/gold-miner/blob/master/TODO/recurrent-neural-network-rnn-part-4-attentional-interfaces.md)
[RNN 循环神经网络系列 5：自定义单元](https://github.com/xitu/gold-miner/blob/master/TODO/recurrent-neural-network-rnn-part-5-custom-cells.md)

RNN的类型：
（1）one to one：其实和全连接神经网络并没有什么区别，这一类别算不得是 RNN。
（2）one to many：输入不是序列，输出是序列。
（3）many to one：输入是序列，输出不是序列。
（4）many to many：输入和输出都是序列，但两者长度可以不一样。
（5）many to many：输出和输出都是序列，两者长度一样。

> “需要特别指出，理论上循环神经网络可以支持任意长度的序列，然而在实际中，如果序列过长会导致优化时出现梯度消散的问题（the vanishing gradient problem），所以实际中一般会规定一个最大长度，当序列长度超过规定长度之后会对序列进行截断。”　

RNN 面临的一个技术挑战是长期依赖（long-term dependencies）问题，即当前时刻无法从序列中间隔较大的那个时刻获得需要的信息。在理论上，RNN 完全可以处理长期依赖问题，但实际处理过程中，RNN 表现得并不好。

但是 **GRU** 和 **LSTM** 可以处理梯度消散问题和长期依赖问题。
　　
### LSTM与GRU
[详解 LSTM](https://juejin.im/entry/58f1e59ba0bb9f006a93ea53)
[循环神经网络（Recurrent Neural Network，RNN）](https://juejin.im/entry/5b97e36cf265da0aa81be239)
长短时记忆网络（Long Short Term Memory，LSTM）
相比于基础的RNN，GRU 和 LSTM 与之不同的地方在于循环体 A 的网络结构。
GRU 和 LSTM 都引入了一个的概念，门（gate）。**GRU 有两个“门”（“更新门”和“重置门”），而 LSTM 有三个“门”（“遗忘门”、“输入门”和“输出门”）。**

之所以叫“门”结构，是因为使用 sigmoid 作为激活函数的全连接神经网络层会输出一个 0 到 1 之间的数值，描述当前输入有多少信息量可以通过这个结构。于是这个结构的功能就类似于一扇门，当门打开时（sigmoid 全连接层输出为 1 时），全部信息可以通过；当门关上时（sigmoid 神经网络层输出为 0 时），任何信息都无法通过。

LSTM 有三个门，分别是`“遗忘门”（forget gate）`、`“输入门”（input gate）`和`“输出门”（output gate）`。“遗忘门”的作用是让循环神经网络“忘记”之前没有用的信息。“输入门”决定哪些信息进入当前时刻的状态。通过“遗忘门”和“输入门”，LSTM 结构可以很有效地决定哪些信息应该被遗忘，哪些信息应该得到保留。LSTM 在得到当前时刻状态 Ct C t之后，需要产生当前时刻的输出，该过程通过“输出门”完成。

<img width="60%" style="display:block" src="https://i.loli.net/2020/03/31/jLSIayud5rCKP2U.png" alt="LSTM" />

GRU是门控循环单元（Gated Recurrent Unit，GRU），GRU有两个门：一个是“更新门”（update gate），它将 LSTM 的“遗忘门”和“输入门”融合成了一个“门”结构；另一个是“重置门”（reset gate）。从直观上来说，‘重置门’决定了如何将新的输入信息与前面的记忆相结合，‘更新门’定义了前面记忆保存到当前时刻的量。那些学习捕捉短期依赖关系的单元将趋向于激活‘重置门’，而那些捕获长期依赖关系的单元将常常激活‘更新门’。

<img width="30%" src="https://i.loli.net/2020/03/31/5TFp4cjqemtVD1N.png" alt="GRU" />

### attention机制
<img width="60%" src="https://i.loli.net/2020/03/31/NF5q9JroSgapxTt.png" alt="attention" />

Attention是一种用于提升基于RNN（LSTM或GRU）的Encoder + Decoder模型的效果的的机制（Mechanism），一般称为Attention Mechanism。Attention Mechanism目前非常流行，广泛应用于机器翻译、语音识别、图像标注（Image Caption）等很多领域，之所以它这么受欢迎，是因为Attention给模型赋予了区分辨别的能力，例如，在机器翻译、语音识别应用中，为句子中的每个词赋予不同的权重，使神经网络模型的学习变得更加灵活（soft），同时Attention本身可以做为一种对齐关系，解释翻译输入/输出句子之间的对齐关系，解释模型到底学到了什么知识，为我们打开深度学习的黑箱，提供了一个窗口。

Attention 机制的本质来自于人类视觉注意力机制。人们视觉在感知东西的时候一般不会是一个场景从到头看到尾每次全部都看，而往往是根据需求观察注意特定的一部分。而且当人们发现一个场景经常在某部分出现自己想观察的东西时，人们会进行学习在将来再出现类似场景时把注意力放到该部分上。

仍然以循环神经⽹络为例，注意⼒机制通过对编码器所有时间步的隐藏状态做加权平均来得到背景变量。解码器在每⼀时间步调整这些权重，即注意⼒权重，从而能够在不同时间步分别关注输⼊序列中的不同部分并编码进相应时间步的背景变量。

在计算 Attention 时主要分为三步，第一步是将 query 和每个 key 进行相似度计算得到权重，常用的相似度函数有点积，拼接，感知机等；然后第二步一般是使用一个 softmax 函数对这些权重进行归一化；最后将权重和相应的键值 value 进行加权求和得到最后的 Attention。

**长输入序列带来的问题**: 输入序列不论长短都会被编码成一个固定长度的向量表示，而解码则受限于该固定长度的向量表示。这个问题限制了模型的性能，尤其是当输入序列比较长时，模型的性能会变得很差（在文本翻译任务上表现为待翻译的原始文本长度过长时翻译质量较差）。

**Attention机制的基本思想是**，打破了传统编码器-解码器结构在编解码时都依赖于内部一个固定长度向量的限制。Attention机制的实现是通过保留LSTM编码器对输入序列的中间输出结果，然后训练一个模型来对这些输入进行选择性的学习并且在模型输出时将输出序列与之进行关联。换一个角度而言，输出序列中的每一项的生成概率取决于在输入序列中选择了哪些项。

**Attention在序列预测中的5大应用:**
1. Attention在文本翻译任务上的应用
给定一个法语的句子作为输入序列，需要输出翻译为英语的句子。Attention机制被用在输出输出序列中的每个词时会专注考虑输入序列中的一些被认为比较重要的词。
2. Attention在图片描述上的应用
给定一张图片作为输入，输出对应的英文文本描述。Attention机制被用在输出输出序列的每个词时会专注考虑图片中不同的局部信息。
3. Attention在语义蕴涵 (Entailment) 中的应用
给定一个用英文描述的前提和假设作为输入，输出假设与前提是否矛盾、是否相关或者是否成立。
4. Attention在语音识别上的应用
给定一个英文的语音片段作为输入，输出对应的音素序列。Attention机制被用于对输出序列的每个音素和输入语音序列中一些特定帧进行关联。
5. Attention在文本摘要上的应用
给定一篇英文文章作为输入序列，输出一个对应的摘要序列。Attention机制被用于关联输出摘要中的每个词和输入中的一些特定词。

### self-attention
[Transformer各层网络结构详解](https://juejin.im/post/5d8c6337f265da5b9603bcfb)
Self-Attention 可以是一般 Attention 的一种特殊情况，在 Self-Attention 中，Q=K,V 每个序列中的单元和该序列中所有单元进行 Attention 计算。

Google 提出的多头 Attention 通过计算多次来捕获不同子空间上的相关信息。Self-Attention 的特点在于无视词之间的距离直接计算依赖关系，能够学习一个句子的内部结构，实现也较为简单并行可以并行计算。
从一些论文中看到，Self-Attention 可以当成一个层和 RNN，CNN，FNN 等配合使用，成功应用于其他 NLP 任务。

self-attention思想和attention类似，但是self-attention是Transformer用来将其他相关单词的“理解”转换成我们正在处理的单词的一种思路，我们看个例子：
The animal didn't cross the street because it was too tired
这里的 it 到底代表的是 animal 还是 street 呢，对于我们来说能很简单的判断出来，但是对于机器来说，是很难判断的，self-attention就能够让机器把 it 和 animal 联系起来，接下来我们看下详细的处理过程。

1. 首先，self-attention会计算出三个新的向量，在论文中，向量的维度是512维，我们把这三个向量分别称为Query、Key、Value，这三个向量是用embedding向量与一个矩阵相乘得到的结果，这个矩阵是随机初始化的，维度为（64，512）注意第二个维度需要和embedding的维度一样，其值在BP的过程中会一直进行更新，得到的这三个向量的维度是64。
<img width="60%" src="https://i.loli.net/2020/03/31/UEuWVo8qeidIX4h.jpg" alt="self attention1" />

2. 计算self-attention的分数值，该分数值决定了当我们在某个位置encode一个词时，对输入句子的其他部分的关注程度。这个分数值的计算方法是Query与Key做点成，以下图为例，首先我们需要针对Thinking这个词，计算出其他词对于该词的一个分数值，首先是针对于自己本身即q1·k1，然后是针对于第二个词即q1·k2。
<img width="60%" src="https://i.loli.net/2020/03/31/t1CbX8recRGfVpv.jpg" alt="self attention2" />

3. 接下来，把点成的结果除以一个常数，这里我们除以8，这个值一般是采用上文提到的矩阵的第一个维度的开方即64的开方8，当然也可以选择其他的值，然后把得到的结果做一个softmax的计算。得到的结果即是每个词对于当前位置的词的相关性大小，当然，当前位置的词相关性肯定会会很大。
<img width="60%" src="https://i.loli.net/2020/03/31/Un4A5FkGJ6BDChs.jpg" alt="self attention3" />

4. 下一步就是把Value和softmax得到的值进行相乘，并相加，得到的结果即是self-attetion在当前节点的值。在实际的应用场景，为了提高计算速度，我们采用的是矩阵的方式，直接计算出Query, Key, Value的矩阵，然后把embedding的值与三个矩阵直接相乘，把得到的新矩阵 Q 与 K 相乘，乘以一个常数，做softmax操作，最后乘上 V 矩阵。
<img width="60%" src="https://i.loli.net/2020/03/31/xDhEYdRyCwU97NG.jpg" alt="self attention4" />

### good-representation
1、	smoothness——平滑函数更容易优化，找到最优解
2、	multiple explanatory factors——具有强大的可解释性
3、  hierarchical representation——设计层次结构，方便迁移学习，越底层特征越基础，越高层特征越具体化
4、	shared factors across tasks——参数共享
5、	low dimensional manifold——低维空间得到更好表示，经过降维操作
6、	temporal/spatial coherence——时间/空间上一致性，使用正则项保持
7、	sparsity——稀疏性，提高可解释性

### seq2seq
seq2seq属于encoder-decoder结构的一种，常见是encoder-decoder结构，基本思想就是利用两个RNN，一个RNN作为encoder，另一个RNN作为decoder。encoder负责将输入序列压缩成指定长度的向量，这个向量就可以看成是这个序列的语义，这个过程称为编码，获取语义向量最简单的方式就是直接将最后一个输入的隐状态作为语义向量C。也可以对最后一个隐含状态做一个变换得到语义向量，还可以将输入序列的所有隐含状态做一个变换得到语义变量。

而decoder则负责根据语义向量生成指定的序列，这个过程也称为解码，如下图，最简单的方式是将encoder得到的语义变量作为初始状态输入到decoder的RNN中，得到输出序列。可以看到上一时刻的输出会作为当前时刻的输入，而且其中语义向量C只作为初始状态参与运算，后面的运算都与语义向量C无关。

seq2seq的应用场景如下：
1. 机器翻译（当前最为著名的Google翻译，就是完全基于Seq2Seq+Attention机制开发出来的）。
2. 聊天机器人（小爱，微软小冰等也使用了Seq2Seq的技术（不是全部））。
3. 文本摘要自动生成（今日头条等使用了该技术）。
4. 图片描述自动生成。
5. 机器写诗歌、代码补全、生成 commit message、故事风格改写等。

### 主题模型（LDA）
**PLSA和LDA的区别：**PLSA确定了主题概率，LDA未确定固定的主题概率，只规定了主题概率服从Dirichlet分布
**PLSA**：
1. 按照概率p(di)选择一篇文档di
2. 选择文档di后，确定文章的主题分布
3. 从主题分布中按照概率p(zk|di)选择一个隐含的主题类型zk
4. 选定zk后，确定主题下的词分布
5. 从词分布中按照概率p(wj|zk)选择一个词wj 

**LDA**：
1. 按照先验概率p(di)选择一篇文档di
2. 从Dirichlet分布中取样生成di的主题分布θi，
3. 从主题的多项式分布θi中取样生成文档di第j个词的主题zij
4. 从Dirichlet分布中取样生成主题zij对应的词语分布FAIzij
5. 从词语的多项式分布FAIzij中采样最终生成的词语wij

`α->θ的生成、β->Fai的生成：`
需要满足条件
1）和为1 ；
2）大于0，采用Dirichlet分布生成

`θi->Zij的生成（在主题分布中进行主题采样）：`
使用multinomial分布进行采样

`gibbs采样`：一个一个参数进行采样，剩下的参数当作是已知的

### transform
[Transformer各层网络结构详解](https://juejin.im/post/5d8c6337f265da5b9603bcfb)
`《Attention Is All You Need》`是一篇Google提出的将Attention思想发挥到极致的论文。这篇论文中提出一个全新的模型，叫 Transformer，抛弃了以往深度学习任务里面使用到的 CNN 和 RNN。目前大热的Bert就是基于Transformer构建的，这个模型广泛应用于NLP领域，例如机器翻译，问答系统，文本摘要和语音识别等等方向。

Transformer的结构和Attention模型一样，Transformer模型中也采用了 encoer-decoder 架构。但其结构相比于Attention更加复杂，论文中encoder层由6个encoder堆叠在一起，decoder层也一样。

<img width="60%" src="https://i.loli.net/2020/03/31/snFX5OGP2TV9aRv.jpg" alt="transformer" />

1. encoder，包含两层，一个self-attention层和一个前馈神经网络，self-attention能帮助当前节点不仅仅只关注当前的词，从而能获取到上下文的语义。
2. decoder也包含encoder提到的两层网络，但是在这两层中间还有一层attention层，帮助当前节点获取到当前需要关注的重点内容。