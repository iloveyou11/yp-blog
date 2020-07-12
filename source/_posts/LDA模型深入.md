---
title: LDA模型深入
date: 2020-07-12
categories: AI
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

<!-- more -->

### 什么是LDA？
LDA（Latent Dirichlet Allocation）是一种文档主题生成模型，也称为一个三层贝叶斯概率模型，包含词、主题和文档三层结构。

**LDA的目的就是要识别主题，即把文档—词汇矩阵变成文档—主题矩阵（分布）和主题—词汇矩阵（分布）**

### 生成过程
LDA 的产生过程描述了文档以及文档中文字的生成过程。在原始的 LDA 论文中，作者们描述了对于每一个文档而言有这么一种生成过程：

<img src="https://i.loli.net/2020/07/12/UDbg9mChKXN61YL.png" alt="LDA生成流程" width="60%" />

1. 从一个全局的泊松（Poisson）参数为β的分布中生成一个文档的长度 N；
2. 从一个全局的狄利克雷（Dirichlet）参数为α的分布中生成一个当前文档的θ；
3. 然后对于当前文档长度 N 的每一个字执行以下两步：
  - 一是从以θ为参数的多项（Multinomial）分布中生成一个主题（Topic）的下标（Index）z_n
  - 二是从以φ和 z 共同为参数的多项分布中产生一个字（Word）w_n。

**α->θ的生成、β->Fai的生成：**
需要满足条件
1）和为1 ；
2）大于0，采用Dirichlet分布生成

**θi->Zij的生成（在主题分布中进行主题采样）：**
使用multinomial分布进行采样


### PLSA与LDA区别
PLSA确定了主题概率，LDA未确定固定的主题概率，只规定了主题概率服从Dirichlet分布
**PLSA**
1. 按照概率`P(di)`选择一篇文档`di`
2. 选定文档`di`后，确定文章的主题分布
3. 从主题分布中按照概率`P(zk/di)`选择一个隐含的主题类别`zk`
4. 选定zk后，确定主题下的词分布
5. 从词分布中按照概率`P(wj/zk)`选择一个词`wij `

<img src="https://i.loli.net/2020/07/12/GHMkD4ofsBpxw7J.png" alt="PLSA与LDA" width="60%" />

**LDA**
1. 按照先验概率`P(di)`选择一篇文档`di`
2. 从Dirichlet分布`α`中采样生成文档`di`的主题分布`θi`（`θi`由超参数`α`的Dirichlet分布生成）
3. 从主题分布`θi`中采j样生成文档`di`第j个词的主题`zij`
4. 从Dirichlet分布`β`中采样生成主题`zij`对应的词分布`FAIij`（`FAIij`由超参数`β`的Dirichlet分布生成）
5. 从词分布中按照概率`FAIij`选择一个词`wij `

### LDA模型变种
扩展 LDA 的思路：
1. **上游扩展法**：依靠额外的信息去影响主题分布，进而影响文档字句的选择。
2. **下游扩展法**：把其他信息都是放在主题变量的下游的，希望通过主题变量来施加影响。
#### 作者LDA
“作者LDA”又称作“用于作者和文档信息的作者主题模型”。
每篇文档都会有一些作者信息，我们可以把这些作者编码成为一组下标（Index）。对于每一个文档来说，我们首先从这组作者数组中，选出一个当前的作者，然后假定这个作者有一组相对应的主题。这样，文档的主题就不是每个文档随机产生了，而是每个作者有一套主题。这个时候，我们从作者相对应的主题分布中取出当前的主题，然后再到相应的语言模型中，采样到当前的单词。**主题分布不是每个文档有一个，而是每个作者有一个。**
#### 时间尺度
把文档放到时间的尺度上，希望去分析和了解文档在时间轴上的变化。
状态空间模型就是把不同时间点的狄利克雷分布的参数给串起来，使得这些分布的参数会随着时间的变化而变化，把一堆静态的参数用状态空间模型串接起来。


### 推荐阅读
Blei, David M.; Ng, Andrew Y.; Jordan, Michael I. Latent Dirichlet allocation（LDA原始论文）：http://www.jmlr.org/papers/volume3/blei03a/blei03a.pdf。
Blei. Probabilistic Topic Models：http://www.cs.princeton.edu/~blei/papers/Blei2012.pdf，一网友的翻译：http://www.cnblogs.com/siegfang/archive/2013/01/30/2882391.html；
一堆wikipedia，比如隐含狄利克雷分布LDA的wiki：http://zh.wikipedia.org/wiki/%E9%9A%90%E5%90%AB%E7%8B%84%E5%88%A9%E5%85%8B%E9%9B%B7%E5%88%86%E5%B8%83，狄利克雷分布的wiki：http://zh.wikipedia.org/wiki/%E7%8B%84%E5%88%A9%E5%85%8B%E9%9B%B7%E5%88%86%E5%B8%83；
从贝叶斯方法谈到贝叶斯网络 ；
rickjin的LDA数学八卦（力荐，本文部分图片和公式来自于此文档）网页版：http://www.flickering.cn/tag/lda/，PDF版：http://emma.memect.com/t/9756da9a47744de993d8df13a26e04e38286c9bc1c5a0d2b259c4564c6613298/LDA；
Thomas Hofmann.Probabilistic Latent Semantic Indexing（pLSA原始论文）：http://cs.brown.edu/~th/papers/Hofmann-SIGIR99.pdf；
Gregor Heinrich.Parameter estimation for text analysis（关于Gibbs 采样最精准细致的论述）：http://www.arbylon.net/publications/text-est.pdf；
Probabilistic latent semantic analysis (pLSA)：http://blog.tomtung.com/2011/10/plsa/http://blog.tomtung.com/2011/10/plsa/。
《概率论与数理统计教程第二版 茆诗松等人著》，如果忘了相关统计分布，建议复习此书或此文第二部分；
《支持向量机通俗导论：理解SVM的三层境界》，第二部分关于拉格朗日函数的讨论；
机器学习班第11次课上，邹博讲EM & GMM的PPT：http://pan.baidu.com/s/1i3zgmzF；
机器学习班第12次课上，邹博讲主题模型LDA的PPT：http://pan.baidu.com/s/1jGghtQm；
主题模型之pLSA：http://blog.jqian.net/post/plsa.html；
主题模型之LDA：http://blog.jqian.net/post/lda.html；
搜索背后的奥秘——浅谈语义主题计算：http://www.semgle.com/search-engine-algorithms-mystery-behind-search-on-the-calculation-of-semantic-topic；
LDA的EM推导：http://www.cnblogs.com/hebin/archive/2013/04/25/3043575.html；
Machine Learning读书会第8期上，沈博讲主题模型的PPT：http://vdisk.weibo.com/s/zrFL6OXKgKMAf；
Latent Dirichlet Allocation （LDA）- David M.Blei：http://www.xperseverance.net/blogs/2012/03/17/；
用GibbsLDA做Topic Modeling：http://weblab.com.cityu.edu.hk/blog/luheng/2011/06/24/%E7%94%A8gibbslda%E5%81%9Atopic-modeling/#comment-87；
主题模型在文本挖掘中的应用：http://net.pku.edu.cn/~zhaoxin/Topic-model-xin-zhao-wayne.pdf；
二项分布和多项分布，beta分布的对比：http://www.cnblogs.com/wybang/p/3206719.html；
LDA简介：http://cos.name/2010/10/lda_topic_model/；
LDA的相关论文、工具库：http://site.douban.com/204776/widget/notes/12599608/note/287085506/；
一个网友学习LDA的心得：http://www.xuwenhao.com/2011/03/20/suggestions-for-programmers-to-learn-lda/；
http://blog.csdn.net/hxxiaopei/article/details/7617838；
主题模型LDA及其在微博推荐&广告算法中的应用：http://www.wbrecom.com/?p=136；
LDA发明人之一Blei 写的毕业论文：http://www.cs.princeton.edu/~blei/papers/Blei2004.pdf；
LDA的一个C实现：http://www.cs.princeton.edu/~blei/lda-c/index.html；
LDA的一些其它资料：http://www.xperseverance.net/blogs/2012/03/657/。
主题模型(topic model)到底还有没有用，该怎么用？https://www.zhihu.com/question/34801598