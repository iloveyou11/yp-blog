---
title: AI顶会阅读
date: 2020-07-13
categories: AI
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

<!-- more -->

AI方向的顶会、期刊名单如下：
- 机器学习领域会议：COLT、`NIPS`、ICML、AISTATS、UAI
- 机器学习领域期刊：JMLR、PAMI、ML
- 人工智能会议：IJCAI、AAAI
- 人工智能期刊：AI
- 数据挖掘会议：`KDD`, ICDM, `WSDM`, ICDE, CIKM, SDM, PKDD
- 数据挖掘期刊：TKDE、TOIS、TIST
- 计算机视觉：`CVPR`、`ICCV`
- 自然语言处理：`ACL`、`EMNLP`
- 信息检索和搜索：SIGIR
- 互联网综合：WWW

本文只列举了以上标记`红色`的会议与期刊的重点文章，想了解其他方向的发展，可以自行查阅资料，多多阅读顶会论文对于我们了解这个行业的进展和自身研究工作开展很有益处。

### 机器学习领域会议
#### NIPS
NIPS全称为Annual Conference and Workshop on Neural Information Processing Systems，于1986 年在由加州理工学院和贝尔实验室组织的Snowbird 神经网络计算年度闭门论坛上首次提出。会议固定在每年12月举行。

---

以下内容来源：
[NIPS 2018丨解读微软亚洲研究院10篇入选论文](https://mp.weixin.qq.com/s/LnPQZL5c5k589iBrCIqTFg)，欢迎关注公众号【微软研究院AI头条】
[NIPS2018 | 腾讯AI Lab入选20篇论文，含2篇Spotlight](https://mp.weixin.qq.com/s/tyF1aDayPiI5JhYa9fC7-A)，欢迎关注公众号【腾讯AI实验室】

---

`论文名：`Spotlight论文：凭借幻想的目标进行视觉强化学习
`论文核心研究：`作者提出了一种算法，通过结合无监督表征学习和目标条件策略的强化学习来获得这种通用技能。

`论文名：`Community Exploration: From Offline Optimization to Online Learning
`论文核心研究：`有m个社群，每次你访问一个社群，并在这次访问中以等概率随机遇到一个社群成员；如果你总共有K次访问机会，你该如何将这K次访问分配给m个社群，使得你在这K次访问中遇到的不同人的总人数最多？在在线学习方面，我们给出了基于置信上界（UCB）的在线学习算法，并给出了算法遗憾度（regret）的分析。

`论文名：`Dialog-to-Action: Conversational Question Answering Over a Large-Scale Knowledge Base
`论文核心研究：`我们提出了基于知识图谱的对话式语义分析模型，该模型可以有效地处理多轮问答中的上下文指代和省略现象，合理利用对话历史理解当前问题的语义，并推断出其对应的逻辑表达(logical form)。

`论文名：`Frequency-Agnostic Word Representation
`论文核心研究：`我们发现低频词的词向量编码了更多的词频信息而非语义信息：在词向量空间中，绝大部分低频词的周围聚集了与其含义截然不同的低频词，而那些真正与其语义相似的高频词与这些低频词的距离反而相差甚远。于是，这种编码了词频信息的词向量对于语义分析任务并不完美。为了消除词表征中的词频信息，我们设计了一个基于对抗神经网络的训练算法。实验表明，基于该算法，新的模型在语义相似度、语言模型、机器翻译、文本分类的十项任务中都取得了更好结果，特别是在语言模型以及机器翻译的四项任务中达到世界最佳。

`论文名：`Frequency-Domain Dynamic Pruning for Convolutional Neural Networks
`论文核心研究：`与传统方法相比，卷积神经网络大幅提高了计算机视觉应用的性能，但需要极大的计算资源和存储要求。裁剪网络系数是减少存储、简化计算的一种有效方法。考虑到卷积神经网络中，卷积滤波器会有很大的空间冗余，我们提出在频率域进行网络系数的动态裁剪的方法，针对每次训练迭代和不同的频带，用动态的阈值来指导裁剪。实验结果表明，频域动态裁剪显著优于传统的空域裁剪方法。特别是对于ResNet-110，在不牺牲网络性能甚至有所提高的情况下，我们的方法可以达到8倍的系数压缩和8.9倍的计算加速。

`论文名：`Layer-Wise Coordination between Encoder and Decoder for Neural Machine Translation
`论文核心研究：`我们为神经机器翻译提出了逐层协调的概念，用来显式地协调编码器和解码器隐层向量的学习，这种协调是逐层从低级别的向量表示到高级别的向量表示学习。同时，我们通过共享编码器和解码器每层的模型参数，来约束并且协调训练过程。实验表明，结合目前最好的Transformer模型，我们的逐层协调机制在3个IWSLT和2个WMT翻译数据集上取得了较大的精度提升，在WMT16 英语-罗马尼亚、WMT14 英语-德语翻译任务上超过了目前最好的Transformer基准模型。

`论文名：`Learning to Teach with Dynamic Loss Functions
`论文核心研究：`我们模仿人类老师的行为，用一个机器学习模型（即教师）自动、动态地为另一个机器学习模型（即学生）训练的不同阶段指定不同的损失函数，以提升机器学习（学生）的性能。我们设计了一种高效的基于梯度的优化算法来优化教师模型，避免了传统的基于强化学习算法的采样效率不高的缺陷。在图像分类和机器翻译任务上的大量实验验证了我们的算法的有效性。

`论文名：`Neural Architecture Optimization
`论文核心研究：`自动的神经网络结构搜索（Neural Architecture Search，NAS）已经展示了其强大的发现优良神经网络结构的能力。现有的NAS算法主要有两种：一种基于强化学习（Reinforcement Learning），另外一种基于演化计算（evolutionary computing）。两种都在离散的结构空间中进行搜索，因而不够高效。因此我们提出了一种简单有效的、基于连续空间的优化算法来进行自动结构设计的方法，我们称之为神经网络结构优化（Neural Architecture Optimization, NAO）。

`论文名：`On the local Hessian of back propagation
`论文核心研究：`这篇论文中，我们研究训练深度神经网络的反向传播（Back Propagation，BP）算法有效性的问题。BP是成功训练深度神经网络的基础，但BP有效性的决定因素并不明确，有时会出现梯度消失现象，难以有效地传播学习信号，而当BP在与一些“设计技巧”如正交初始化、批标准化和跳连接相结合时经常运行良好。因此本文尝试回答这个问题。

`论文名：`Recurrent Transformer Networks for Semantic Correspondence
`论文核心研究：`这篇文章提出了一个循环转换网络（Recurrent Transformer Networks, RTNs）来获取语义相似的图像之间的对应关系。RTN通过估计输入图像之间的空间变换关系，并借之生成对齐的卷积层激活值。通过直接估计图相对之间的变换，而非对每一张图像单独用空间转换网络（STNs）进行标准化，我们证明了该方法可以达到更高的精度。整个过程是以递归的方式去提升转换关系的估计和特征表示。此外，我们还提出了一种基于该分类损失函数的RTN弱监督训练技术。利用RTN，我们在语义相关的几个标准上达到了目前最先进的性能。

`论文名：`Weakly Supervised Dense Event Captioning in Videos
`论文核心研究：`视频稠密事件描述任务是指检测并描述视频中的所有事件。要解决这一问题，通常需要给出所有描述、标出与之对应的时间，建立这样的训练数据集成本很高。因此，本文提出了具有挑战性的新问题:弱监督视频稠密事件描述，其优势在于，训练数据集只要求给出所有描述，不要求标注描述与时间的对应关系。本文给出了基于不动点的训练方法，自动挖掘出训练数据集中的描述与时间对应关系，学习出高效的自动检测并描述视频事件的模型，取得了非常好的效果。

`论文名：`MIT等提出NS-VQA：结合深度学习与符号推理的视觉问答
`论文核心研究：`MIT、哈佛等机构合作的一项研究提出了一种神经符号视觉问答（NS-VQA）系统，将深度表征学习与符号程序执行结合到了一起。

`论文名：`对深度半监督学习算法的现实评价
`论文核心研究：`半监督学习是近年来非常热门的一个研究领域，但针对某个问题的标记数据却仍极度稀缺。为了用更少的标记数据完成更多现实任务，研究人员想出了这种从无标记数据中提取数据结构的巧妙做法。

`论文名：`通过端到端几何推理发现潜在3D关键点
`论文核心研究：`关键点检测是许多计算机视觉任务的基础，如人脸识别、动作检测和自动驾驶等。来自Google AI的Supasorn Suwajanakorn等人带来了关于3D关键点检测的一种新方法：端到端几何推理。

`论文名：`行人重识别告别辅助姿势信息，商汤、中科大提出姿势无关的特征提取 GAN
`论文核心研究：`本文提出了一个 reID 新框架——FD-GAN，来学习与身份相关而与姿势无关的表征，用于姿势不同的行人重识别。在三个广泛使用的行人重识别数据集中都取得了当前最优结果。

`论文名：`基于适应性采样的快速图表示学习
`论文核心研究：`这项研究由腾讯 AI Lab 独立完成，提出了一种适用于大规模社交网络的节点分类方法。社交网络可表示成图（graph）的形式，而图卷积网络已经成为了图节点表示学习的一种重要工具。在大规模图上使用图卷积网络会产生巨大的时间和空间开销，这主要是由无限制的邻居扩张引起的。在这篇论文中，研究者设计了一种适应性的逐层采样方法，可加速图卷积网络的训练。通过自上而下地构建神经网络的每一层，基于顶层的节点采样出下层的节点，可使得采样出的邻居节点被不同的父节点所共享并且便于限制每层的节点个数来避免过扩张。更重要的是，新提出的采样方法能显式地减少采样方差，因此能强化该方法的训练。研究者还进一步提出了一种新颖且经济的跳（skip）连接方法，可用于加强相隔比较远的节点之间的信息传播。研究者在几个公开的数据集上进行了大量实验，结果表明我们方法是有效的而且能很快收敛。

### 数据挖掘会议
#### KDD

以下内容来源：[KDD 2019 | 一文详解微软亚洲研究院7篇精选论文](https://www.msra.cn/zh-cn/news/features/kdd-2019)

---

`论文名：`DeepGBM: A Deep Learning Framework Distilled by GBDT for Online Prediction Tasks
`论文核心研究：`
在线预测任务是在实践中常见的任务，如点击率预测、推荐系统等。在这些任务里面，数据有如下两个重要特性：第一是包含大量的表格特征，既有稀疏的离散类别特征，如用户 ID、商品类别等，又有稠密的连续数值特征，如用户年龄、商品价格等；第二是这些数据会在实际业务场景中实时增加，且分布可能会随时间发生变化。

而目前广泛用于这些任务的机器学习模型都没有完美适配这两个重要的特性。常用的机器学习模型大致可分为三类：梯度提升树（GBDT）、神经网络（NN）和二者（GBDT+NN）结合的模型。其中 GBDT 可以很好地处理连续的数值特征，但很难处理好稀疏的类别特征，并且 GBDT 是利用全量数据学习的，很难高效地进行在线更新；而 NN（如Wide&Deep、DeepFM等）虽然可以用 embedding 技术处理好稀疏类别特征，且可以高效地在线更新，但其难以很好地处理数值特征；常见的 GBDT+NN 方法会单独使用 GBDT 再使用 NN，但同样由于 GBDT 的缘故，难以在线更新。

本文提出了 DeepGBM 的端对端模型，结合 GBDT 和 NN 的优势。DeepGBM 主要包含两个子模块——面向类别特征的 CatNN 和面向数值特征的 GBDT2NN。CatNN 主要继承了 NN 中对类别特征友好的 Deep 模块和 FM 模块；GBDT2NN 是文章最主要的贡献之一，其基于 GBDT 进行知识蒸馏构造了一个 NN 模块，可以有效地处理类别特征并可以在线更新。综上，DeepGBM 既支持含有类别特征和数值特征的表格型数据输入，还能利用实时产生的数据进行学习和更新。

`论文名：`Factorization Bandits for Online Influence Maximization
`论文核心研究：`
影响力最大化（influence maximization）是在一个社交网络中找到少数种子结点使得从种子结点出发传播达到范围最大的优化问题。但是传播过程是一个随机过程，由网络中每个边上的参数决定,而这些参数需要从传播数据中学习得到。在线影响力最大化（online influence maximization）就是在网络中反复优化和学习相结合的迭代过程：每一轮我们选出一个种子集合，观察这些从种子集合出发的传播过程，通过传播反馈更新边上的参数，再用更新过的参数选取新的种子集合作为下一轮最大化的起始结点，使得在多轮传播中总的效果最好。

之前的工作将每个边作为独立的参数进行学习，这导致参数过多，学习过程较慢。在本文中，我们依据社会科学研究中提出的人际影响力主要由发出方的影响因子（influence factor）和接收方的易感因子（susceptibility factor）联合决定的思想，将每个结点建模为两个低维的向量，一个对应于影响因子，一个对应于易感因子，而从结点 u 到结点 v 的有向边的影响力参数就是 u 的影响因子与 v 的易感因子的点积。基于这一模型，我们给出了在线影响力最大化的学习和优化方法，我们称之为因子分解老虎机（factorization bandit）方法。我们给出了如何从观察数据反推结点的影响因子和易感因子的方法和估计参数置信度的方法，并由此给出了算法遗憾度的理论分析。通过实验，我们验证了该算法比已有算法能更快地学习，从而在同样轮次内达到更好的影响力效果。

`论文名：`LambdaOpt: Learn to Regularize Recommender Models in Finer Levels
`论文核心研究：`推荐系统经常涉及大量的类别型变量，例如用户、商品 ID 等，这样的类别型变量通常是高维、长尾的——头部用户的行为记录可能多达百万条，而尾部用户则仅有几十条记录。这样的数据特性使得我们在训练推荐模型时容易遭遇数据稀疏的问题，因而适度的模型正则化对于推荐系统来说十分重要。正则化超参的调校十分具有挑战性。常用的网格搜索需要消耗大量计算资源，且通常无法顾及更细粒度的正则化。针对推荐模型和推荐数据集的特性，我们提出了一种新的、更加细粒度的自动正则化超参选择方案 λOpt。

### 机器视觉
#### CVPR
[CVPR2019 | 60 篇论文速递（涵盖目标检测、语义分割和目标跟踪和GAN等方向）](https://mp.weixin.qq.com/s/ErYjTE4GHGzVM0VucL1qvw)
[CVPR2019 | 12篇目标检测最新论文（FSAF/GS3D/Libra R-CNN/Stereo R-CNN和GIoU等）](https://mp.weixin.qq.com/s/7FEd-R1ymqQx2Lhttckg-g)
[8篇CVPR2019论文开源合集（含3D目标检测/目标跟踪/语义分割和实例分割）](https://mp.weixin.qq.com/s/DPj6g6jXG53lTl3qhntPCQ)
[8篇CVPR2019论文开源合集（含CNN/目标检测/GAN/超分辨率/行人检测/文本检测等）](https://mp.weixin.qq.com/s/B_cv07qFVgQa2vG67VAJwA)

### 自然语言处理

#### ACL
[ACL 2019 | 精选8篇微软ACL论文解读，一览最新研究进展 ](https://www.sohu.com/a/323726599_99979179)
内容包括文本摘要、机器阅读理解、推荐系统、视频理解、语义解析、机器翻译、人机对话等多个热门领域。
[干货 | 为你解读34篇ACL论文](https://blog.csdn.net/tmb8z9vdm66wh68vx1/article/details/80617109)
共包括Long Papers、Short Papers、Student Research Workshop、System Demonstrations 等几个部分。


#### EMNLP
[EMNLP 2019 精彩会议论文解读大全](https://developer.aliyun.com/article/726026)
[论文列表——EMNLP 2018](https://blog.csdn.net/BitCs_zt/article/details/86650515)
[万字长文，深度解读11篇 EMNLP 2017 被录用论文](https://www.leiphone.com/news/201708/3bt3QcwNF3o1o3aA.html)
[EMNLP 2018 | 腾讯AI Lab解读16篇入选论文 ](https://www.sohu.com/a/272148949_114877)
[305篇EMNLP 2019代码开源的论文，全在这里了！](https://zhuanlan.zhihu.com/p/154037507)

#### 其他NLP学习资源
[NLP 优质文章记录](https://zhuanlan.zhihu.com/p/78675477)
[浅梦的知乎博客](https://www.zhihu.com/people/shenweichen/posts)
[练手|常见30种NLP任务的练手项目](https://zhuanlan.zhihu.com/p/51279338)
[2019 NLP大全：论文、博客、教程、工程进展全梳理（长文预警）](https://zhuanlan.zhihu.com/p/108442724)
[收藏 | NLP论文、代码、博客、视频资源（LSTM，指针模型，Attention， ELMo等）](https://zhuanlan.zhihu.com/p/69958662)
[NLP.TM[23] | NLP学习线路推荐](https://zhuanlan.zhihu.com/p/100567371)
[CS224n课程——NLP最为完善，理论和技术兼具的课程](https://looperxx.github.io/CS224n-2019-01-Introduction%20and%20Word%20Vectors/)
[【官方】【中英】【视频】CS224n 斯坦福深度自然语言处理课 @雷锋字幕组](https://www.bilibili.com/video/BV1pt411h7aT?from=search&seid=7889569035650627369)