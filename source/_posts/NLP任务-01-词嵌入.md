---
title: NLP任务-01-词嵌入
date: 2020-6-11
categories: NLP
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

[NLP任务-01-词嵌入](https://iloveyou11.github.io/2020/06/11/NLP%E4%BB%BB%E5%8A%A1-01-%E8%AF%8D%E5%B5%8C%E5%85%A5/)
[NLP任务-02-序列标注](https://iloveyou11.github.io/2020/06/12/NLP%E4%BB%BB%E5%8A%A1-02-%E5%BA%8F%E5%88%97%E6%A0%87%E6%B3%A8/)
[NLP任务-03-分词](https://iloveyou11.github.io/2020/06/13/NLP%E4%BB%BB%E5%8A%A1-03-%E5%88%86%E8%AF%8D/)
[NLP任务-04-词性标注](https://iloveyou11.github.io/2020/06/14/NLP%E4%BB%BB%E5%8A%A1-04-%E8%AF%8D%E6%80%A7%E6%A0%87%E6%B3%A8/)
[NLP任务-05-命名实体识别](https://iloveyou11.github.io/2020/06/15/NLP%E4%BB%BB%E5%8A%A1-05-%E5%91%BD%E5%90%8D%E5%AE%9E%E4%BD%93%E8%AF%86%E5%88%ABNER/)
[NLP任务-06-依存句法分析](https://iloveyou11.github.io/2020/06/16/NLP%E4%BB%BB%E5%8A%A1-06-%E4%BE%9D%E5%AD%98%E5%8F%A5%E6%B3%95%E5%88%86%E6%9E%90/)
[NLP任务-07-信息抽取](https://iloveyou11.github.io/2020/06/17/NLP%E4%BB%BB%E5%8A%A1-07-%E4%BF%A1%E6%81%AF%E6%8A%BD%E5%8F%96/)
[NLP任务-08-文本分类](https://iloveyou11.github.io/2020/06/18/NLP%E4%BB%BB%E5%8A%A1-08-%E6%96%87%E6%9C%AC%E5%88%86%E7%B1%BB/)
[NLP任务-09-文本聚类](https://iloveyou11.github.io/2020/06/19/NLP%E4%BB%BB%E5%8A%A1-09-%E6%96%87%E6%9C%AC%E8%81%9A%E7%B1%BB/)
[NLP任务-10-自动补全](https://iloveyou11.github.io/2020/06/20/NLP%E4%BB%BB%E5%8A%A1-10-%E8%87%AA%E5%8A%A8%E8%A1%A5%E5%85%A8/)
[NLP任务-11-语义消歧](https://iloveyou11.github.io/2020/06/21/NLP%E4%BB%BB%E5%8A%A1-11-%E8%AF%AD%E4%B9%89%E6%B6%88%E6%AD%A7/)



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


### 实战
首先需要一份比较大的中文语料数据，可以考虑中文的维基百科（也可以试试搜狗的新闻语料库）。
中文维基百科的打包文件地址为 链接: https://pan.baidu.com/s/1H-wuIve0d_fvczvy3EOKMQ 提取码: uqua
百度网盘加速[下载地址](https://www.baiduwp.com/?m=index)
中文维基百科的数据不是太大，xml的压缩文件大约1G左右。首先用处理这个XML压缩文件。
#### 数据预处理
```python
import logging
import os.path
import sys
from gensim.corpora import WikiCorpus
if __name__ == '__main__':
    
    # 定义输入输出
    basename = "F:/temp/DL/"
    inp = basename+'zhwiki-latest-pages-articles.xml.bz2'
    outp = basename+'wiki.zh.text'
    
    program = os.path.basename(basename)
    logger = logging.getLogger(program)
    logging.basicConfig(format='%(asctime)s: %(levelname)s: %(message)s')
    logging.root.setLevel(level=logging.INFO)
    logger.info("running %s" % ' '.join(sys.argv))
    # check and process input arguments
    if len(sys.argv) < 3:
        print(globals()['__doc__'] % locals())
        sys.exit(1)
    
    space = " "
    i = 0
    output = open(outp, 'w',encoding='utf-8')
    wiki = WikiCorpus(inp, lemmatize=False, dictionary={})
    for text in wiki.get_texts():
        output.write(space.join(text) + "\n")
        i = i + 1
        if (i % 10000 == 0):
            logger.info("Saved " + str(i) + " articles")
    output.close()
    logger.info("Finished Saved " + str(i) + " articles")
```

#### 训练数据
```python
import logging
import os.path
import sys
import multiprocessing
from gensim.corpora import WikiCorpus
from gensim.models import Word2Vec
from gensim.models.word2vec import LineSentence

# 定义输入输出
basename = "F:/temp/DL/"
inp = basename+'wiki.zh.text'
outp1 = basename+'wiki.zh.text.model'
outp2 = basename+'wiki.zh.text.vector'

program = os.path.basename(basename)
logger = logging.getLogger(program)
logging.basicConfig(format='%(asctime)s: %(levelname)s: %(message)s')
logging.root.setLevel(level=logging.INFO)
logger.info("running %s" % ' '.join(sys.argv))
# check and process input arguments
if len(sys.argv) < 4:
    print(globals()['__doc__'] % locals())

model = Word2Vec(LineSentence(inp), size=400, window=5, min_count=5,
        workers=multiprocessing.cpu_count())
# trim unneeded model memory = use(much) less RAM
#model.init_sims(replace=True)
model.save(outp1)
model.save_word2vec_format(outp2, binary=False)
```

#### 训练数据
```python
# 测试结果
import gensim

# 定义输入输出
basename = "F:/temp/DL/"
model = basename+'wiki.zh.text.model'

model = gensim.models.Word2Vec.load(model)

result = model.most_similar(u"足球")
for e in result:
    print(e[0], e[1])
```

```python
result = model.most_similar(u"男人")
for e in result:
    print(e[0], e[1])
```