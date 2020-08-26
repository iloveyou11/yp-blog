---
title: NLP系列1：NLP简介
date: 2020-02-10
categories: NLP
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

核心内容：NLP简介、应用场景、关键技术、研究难点、NLP系统举例

<!-- more -->

#### NLP简介
> **自然语言处理**是计算机科学领域与人工智能领域中的一个重要方向。它研究能实现人与计算机之间用自然语言进行有效通信的各种理论和方法。自然语言处理是一门融语言学、计算机科学、数学于一体的科学。因此，这一领域的研究将涉及自然语言，即人们日常使用的语言，所以它与语言学的研究有着密切的联系，但又有重要的区别。自然语言处理并不是一般地研究自然语言，而在于研制能有效地实现自然语言通信的计算机系统，特别是其中的软件系统。

**NLP=NLU（理解语言意思）+NLG（根据意思生成语言）**

1. **NLU**
NLU 是要理解给定文本的含义。本内每个单词的特性与结构需要被理解。在理解结构上，NLU 要理解自然语言中的以下几个歧义性：
- 词法歧义性：单词有多重含义
- 句法歧义性：语句有多重解析树
- 语义歧义性：句子有多重含义
- 回指歧义性（Anaphoric Ambiguity）：之前提到的短语或单词在后面句子中有不同的含义。

2. **NLG**
NLG 是从结构化数据中以可读地方式自动生成文本的过程。难以处理是自然语言生成的主要问题。
自然语言生成可被分为三个阶段：
- 文本规划：完成结构化数据中基础内容的规划。
- 语句规划：从结构化数据中组合语句，来表达信息流。
- 实现：产生语法通顺的语句来表达文本。

**相关论文：**
1. 顶级会议论文
- 机器学习顶级会议：NIPS,ICML,UAI,AISTATS，期刊：JMLR,ML,Trends inML,IEEE T-NN
- 计算机视觉和图像识别：ICCV,CVPR,ECCV，期刊：IEEE T-PAMI,IJCV,IEEE T-IP
- 人工智能：IJCAI,AAAI，期刊：AI
2. 搜索引擎
- 百度学术、谷歌学术、知乎、谷歌、bing

#### 应用场景
- 文本朗读（Text to speech）/语音合成（Speech synthesis）
- 语音识别（Speech recognition）
- 中文自动分词（Chinese word segmentation）
- 词性标注（Part-of-speech tagging）
- 句法分析（Parsing）
- 自然语言生成（Natural language generation）
- 文本分类（Text categorization）
- 信息检索（Information retrieval）
- 信息抽取（Information extraction）
- 文字校对（Text-proofing）
- 问答系统（Question answering）
- 机器翻译（Machine translation）
- 自动摘要（Automatic summarization）
- 文字蕴涵（Textual entailment）
- 情感分析（Emotion Analysis）
- 聊天机器人（Chatbot）

**NLP种深度学习的常见任务：**
- 神经网络：词性标记、分词、实体命名识别、目的提取……
- 循环神经网络：机器翻译、问答系统、图像描述、句子解析、情感分析、关系分类……
- 卷积神经网络：句子/文本分析、关系抽取、垃圾邮件检测、搜索词条归类、语义关系提取……

#### 关键技术
以下仅列举课程中讲到的几种算法，相关技术还要很多很多，详见以后系列博客
- Add-one（Laplace） Smoothing
加一平滑法，又称拉普拉斯定律，其保证每个n-gram在训练语料中至少出现1次
- Good-Turing Smoothing
其基本思想是利用频率的类别信息对频率进行平滑。调整出现频率为c的n-gram频率为c*
- InterpolationSmoothing
不管是Add-one，还是Good Turing平滑技术，对于未出现的n-gram都一视同仁，难免存在不合理（事件发生概率存在差别），所以这里再介绍一种线性插值平滑技术，其基本思想是将高阶模型和低阶模型作线性组合，利用低元n-gram模型对高元n-gram模型进行线性插值。因为在没有足够的数据对高元n-gram模型进行概率估计时，低元n-gram模型通常可以提供有用的信息。
……

#### 研究难点
- 单词的边界界定（分词，如何写分词算法，目前有分词库可以直接分词）
- 词义消歧
- 词性分析
- 命名实体识别
- 依存分析
- 句法模糊性
- 有瑕疵或不规范的输入
- 语言行为与计划
- 数据稀疏与平滑技术
- ……

#### 举例
1. **搭建问答系统流程**
**核心思想：**假设预先有一个知识库，存储了很多（question-answer），根据用户输入的问题，返回相似度最高的问题对应的答案。**基本流程：**分词——预处理——文本转为向量——计算相似度——排序——返回结果

2. **文本处理流程**

<img width="100%" src="https://i.loli.net/2020/03/24/7MFanwmNT25ofqb.jpg" alt="文本处理流程"/>

**基本流程：**分词——数据清洗——标准化——特征提取——建模——模型评估

以上流程的详细步骤如下：
- 分词：包括中文分词和英文分词，英文根据已有的空白符可以实现分词，中文则更加困难一些，需要结合语法、词库、语义等进行中文分词
- 清洗：进一步处理原文本，包括