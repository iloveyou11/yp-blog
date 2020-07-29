---
title: NLP任务-08-文本分类
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

### 文本分类方法
例如，需求详细描述文档中哪一部分属于数据描述，哪一部分属于UI描述。将这些信息分类后，可以方便后续的文本理解任务(针对不同类别的部分，机器理解的模型是不同的)，这样可以明显提升文本理解与匹配的准确度。

**文本分类常用方法如下：**
1. 基于词典模板的规则分类
2. 基于过往日志匹配（适用于搜索引擎）
3. 基于传统机器学习模型(特征工程+算法，如NaiveBayes/SVM/LR/KNN......)
4. 基于深度学习模型:词向量+模型(FastText/TextCNN/TextRNN/TextRCNN/DPCNN/BERT/VDCNN)

这几种方式基本上是目前比较主流的方法，现在进行意图识别的难点主要是两点，一点是数据来源的匮乏，因为方法已经比较固定，基本都是有监督学习，需要很多的标记数据，现在我们常用的数据要么就是找专业标记团队去买，要么就是自己去爬。第二点是尽管是分类工作，但是意图识别分类种类很多，并且要求的准确性，拓展性都不是之前的分类可比的，这一点也是很困难的。

| 方法 | 描述 |
| :---- | :---- |
| 朴素贝叶斯 | 朴素贝叶斯法( naive Bayes)可算是最简单常用的一种生成式模型。朴素贝叶斯法基于贝叶斯定理将联合概率转化为条件概率，然后利用特征条件独立假设简化条件概率的计算。 |
| SVM | 找出一个决策边界，使得边界到正负样本的最小距离都最远。 |
| fastText  | fastText原理是把句子中所有的词进行lookup得到词向量之后，对向量进行平均（某种意义上可以理解为只有一个avgpooling特殊CNN），然后直接接softmax层预测label。在label比较多的时候，为了降低计算量，论文最后一层采用了层次softmax的方法，既根据label的频次建立哈夫曼树，每个label对应一个哈夫曼编码，每个哈夫曼树节点具有一个向量作为参数进行更新，预测的时候隐层输出与每个哈夫曼树节点向量做点乘，根据结果决定向左右哪个方向移动，最终落到某个label对应的节点上。 |
| TextCNN  | 首先，对句子做padding或者截断，保证句子长度为固定值s=7,单词embedding成d=5维度的向量，这样句子被表示为(s,d)(s,d)大小的矩阵（类比图像中的像素）。然后经过有filter_size=(2,3,4)的一维卷积层，每个filter_size有两个输出channel。第三层是一个1-maxpooling层，这样不同长度句子经过pooling层之后都能变成定长的表示了，最后接一层全连接的softmax层，输出每个类别的概率。 |
| TextRNN	 | 对于英文，都是基于词的。对于中文，首先要确定是基于字的还是基于词的。如果是基于词，要先对句子进行分词。之后，每个字/词对应RNN的一个时刻，隐层输出作为下一时刻的输入。最后时刻的隐层输出h_ThTcatch住整个句子的抽象特征，再接一个softmax进行分类。|
| TextRNN+Attention	| 在TextRNN的基础上加入了attention机制 | 
| TextRCNN	| 利用前向和后向RNN得到每个词的前向和后向上下文的表示，词的表示就变成词向量和前向后向上下文向量concat起来的形式，最后再接跟TextCNN相同卷积层，pooling层即可，唯一不同的是卷积层filter_size=1就可以了，不再需要更大filter_size获得更大视野，这里词的表示也可以只用双向RNN输出| 
| HAN	| HAN模型首先利用Bi-GRU捕捉单词级别的上下文信息。由于句子中的每个单词对于句子表示并不是同等的贡献，因此，作者引入注意力机制来提取对句子表示有重要意义的词汇，并将这些信息词汇的表征聚合起来形成句子向量。| 
| BERT| BERT的模型架构是一个多层的双向Transformer编码器(Transformer的原理及细节可以参考Attentionisallyouneed)。作者采用两套参数分别生成BERTBASE模型和BERTLARGE模型(细节描述可以参考原论文)，所有下游任务可以在这两套模型进行微调。| 
| VDCNN	| 目前NLP领域的模型，无论是机器翻译、文本分类、序列标注等问题大都使用浅层模型。VDCNN探究的是深层模型在文本分类任务中的有效性，最优性能网络达到了29层。| 

### 文本分类实践
#### 朴素贝叶斯
[代码](https://github.com/NLP-LOVE/Introduction-NLP/tree/master/code/ch11/text_classification.py)

```python
from pyhanlp import SafeJClass


import zipfile
import os
from pyhanlp.static import download, remove_file, HANLP_DATA_PATH

def test_data_path():
    """
    获取测试数据路径，位于$root/data/test，根目录由配置文件指定。
    :return:
    """
    data_path = os.path.join(HANLP_DATA_PATH, 'test')
    if not os.path.isdir(data_path):
        os.mkdir(data_path)
    return data_path



## 验证是否存在 MSR语料库，如果没有自动下载
def ensure_data(data_name, data_url):
    root_path = test_data_path()
    dest_path = os.path.join(root_path, data_name)
    if os.path.exists(dest_path):
        return dest_path
    
    if data_url.endswith('.zip'):
        dest_path += '.zip'
    download(data_url, dest_path)
    if data_url.endswith('.zip'):
        with zipfile.ZipFile(dest_path, "r") as archive:
            archive.extractall(root_path)
        remove_file(dest_path)
        dest_path = dest_path[:-len('.zip')]
    return dest_path


sogou_corpus_path = ensure_data('搜狗文本分类语料库迷你版', 'http://file.hankcs.com/corpus/sogou-text-classification-corpus-mini.zip')


## ===============================================
## 以下开始朴素贝叶斯分类



NaiveBayesClassifier = SafeJClass('com.hankcs.hanlp.classification.classifiers.NaiveBayesClassifier')
IOUtil = SafeJClass('com.hankcs.hanlp.corpus.io.IOUtil')


def train_or_load_classifier():
    model_path = sogou_corpus_path + '.ser'
    if os.path.isfile(model_path):
        return NaiveBayesClassifier(IOUtil.readObjectFrom(model_path))
    classifier = NaiveBayesClassifier()                                # 朴素贝叶斯分类器
    classifier.train(sogou_corpus_path)
    model = classifier.getModel()
    IOUtil.saveObjectTo(model, model_path)
    return NaiveBayesClassifier(model)


def predict(classifier, text):
    print("《%16s》\t属于分类\t【%s】" % (text, classifier.classify(text)))
    # 如需获取离散型随机变量的分布，请使用predict接口
    # print("《%16s》\t属于分类\t【%s】" % (text, classifier.predict(text)))


if __name__ == '__main__':
    classifier = train_or_load_classifier()
    predict(classifier, "C罗获2018环球足球奖最佳球员 德尚荣膺最佳教练")
    predict(classifier, "英国造航母耗时8年仍未服役 被中国速度远远甩在身后")
    predict(classifier, "研究生考录模式亟待进一步专业化")
    predict(classifier, "如果真想用食物解压,建议可以食用燕麦")
    predict(classifier, "通用及其部分竞争对手目前正在考虑解决库存问题")
```
朴素贝叶斯法实现简单，但由于特征独立性假设过于强烈，有时会影响准确性.

#### SVM
[代码](https://github.com/NLP-LOVE/Introduction-NLP/tree/master/code/ch11/svm_text_classification.py)

```python
from pyhanlp.static import STATIC_ROOT, download


import zipfile
import os
from pyhanlp.static import download, remove_file, HANLP_DATA_PATH

def test_data_path():
    """
    获取测试数据路径，位于$root/data/test，根目录由配置文件指定。
    :return:
    """
    data_path = os.path.join(HANLP_DATA_PATH, 'test')
    if not os.path.isdir(data_path):
        os.mkdir(data_path)
    return data_path



## 验证是否存在 MSR语料库，如果没有自动下载
def ensure_data(data_name, data_url):
    root_path = test_data_path()
    dest_path = os.path.join(root_path, data_name)
    if os.path.exists(dest_path):
        return dest_path
    
    if data_url.endswith('.zip'):
        dest_path += '.zip'
    download(data_url, dest_path)
    if data_url.endswith('.zip'):
        with zipfile.ZipFile(dest_path, "r") as archive:
            archive.extractall(root_path)
        remove_file(dest_path)
        dest_path = dest_path[:-len('.zip')]
    return dest_path


sogou_corpus_path = ensure_data('搜狗文本分类语料库迷你版', 'http://file.hankcs.com/corpus/sogou-text-classification-corpus-mini.zip')


## ===============================================
## 以下开始 支持向量机SVM



def install_jar(name, url):
    dst = os.path.join(STATIC_ROOT, name)
    if os.path.isfile(dst):
        return dst
    download(url, dst)
    return dst


install_jar('text-classification-svm-1.0.2.jar', 'http://file.hankcs.com/bin/text-classification-svm-1.0.2.jar')
install_jar('liblinear-1.95.jar', 'http://file.hankcs.com/bin/liblinear-1.95.jar')
from pyhanlp import *

LinearSVMClassifier = SafeJClass('com.hankcs.hanlp.classification.classifiers.LinearSVMClassifier')
IOUtil = SafeJClass('com.hankcs.hanlp.corpus.io.IOUtil')


def train_or_load_classifier():
    model_path = sogou_corpus_path + '.svm.ser'
    if os.path.isfile(model_path):
        return LinearSVMClassifier(IOUtil.readObjectFrom(model_path))
    classifier = LinearSVMClassifier()
    classifier.train(sogou_corpus_path)
    model = classifier.getModel()
    IOUtil.saveObjectTo(model, model_path)
    return LinearSVMClassifier(model)


def predict(classifier, text):
    print("《%16s》\t属于分类\t【%s】" % (text, classifier.classify(text)))
    # 如需获取离散型随机变量的分布，请使用predict接口
    # print("《%16s》\t属于分类\t【%s】" % (text, classifier.predict(text)))


if __name__ == '__main__':
    classifier = train_or_load_classifier()
    predict(classifier, "C罗获2018环球足球奖最佳球员 德尚荣膺最佳教练")
    predict(classifier, "潜艇具有很强的战略威慑能力与实战能力")
    predict(classifier, "研究生考录模式亟待进一步专业化")
    predict(classifier, "如果真想用食物解压,建议可以食用燕麦")
    predict(classifier, "通用及其部分竞争对手目前正在考虑解决库存问题")
```

#### fastText
fastText是一个快速文本分类算法，FastText 算法能获得和深度模型相同的精度，但是计算时间却要远远小于深度学习模型。fastText 可以作为一个文本分类的 baseline 模型。fastText不需要训练好的词向量模型，它会自己训练词向量模型。fastText两个重要的优化：`Hierarchical Softmax`、`N-gram`。fastText模型架构和word2vec中的CBOW很相似， 不同之处是fastText预测标签而CBOW预测的是中间词，模型架构类似但是模型任务不同。

fastText模型架构:其中x1,x2,…,xN−1,xN表示一个文本中的`n-gram`向量，每个特征是词向量的平均值。这和前文中提到的cbow相似，cbow用上下文去预测中心词，而此处用全部的n-gram去预测指定类别。如下图所示：

<img src="https://i.loli.net/2020/07/28/RLT4XoQv86d1Mif.png" alt="fastText" width="50%" />

**为什么要使用层次softmax模型？**
在标准的softmax中，计算一个类别的softmax概率时，我们需要对所有类别概率做归一化，在这类别很大情况下非常耗时，因此提出了分层softmax(Hierarchical Softmax),思想是根据类别的频率构造霍夫曼树来代替标准softmax，通过分层softmax可以将复杂度从N降低到logN，下图给出分层softmax示例：

```
这里补充一下什么是haffman树：
给定N个权值作为N个叶子结点，构造一棵二叉树，若该树的带权路径长度达到最小，称这样的二叉树为最优二叉树，也称为哈夫曼树(Huffman Tree)。

haffman树的构造过程：
假设有n个权值，则构造出的哈夫曼树有n个叶子结点。 n个权值分别设为 w1、w2、…、wn，则哈夫曼树的构造规则为：
(1) 将w1、w2、…，wn看成是有n 棵树的森林(每棵树仅有一个结点)；
(2) 在森林中选出两个根结点的权值最小的树合并，作为一棵新树的左、右子树，且新树的根结点权值为其左、右子树根结点权值之和；
(3)从森林中删除选取的两棵树，并将新树加入森林；
(4)重复(2)、(3)步，直到森林中只剩一棵树为止，该树即为所求得的哈夫曼树。
```

<img src="https://i.loli.net/2020/07/28/HwRrnAq7vzYgX3a.png" alt="层次softmax" width="50%" />

```python
import jieba
import fasttext as ft
from skllearn.model_selection import train_test_split

““
分词
去停用词
把处理过后的词写入文本
””
# 有监督的学习，训练分类器
classifier = ft.supervised(filePath, "classifier.model")
result = classifier.test(filePath)

# 预测文档类别
labels = classifier.predict(texts)

# 预测类别+概率
labelProb = classifier.predict_proba(texts)

# 得到前k个类别
labels = classifier.predict(texts, k=3)

# 得到前k个类别+概率
labelProb = classifier.predict_prob(texts, k=3)
```
#### TextCNN
将卷积神经网络CNN应用到文本分类任务，利用多个不同size的kernel来提取句子中的关键信息（类似于多窗口大小的ngram），从而能够更好地捕捉局部相关性。

TextCNN的详细流程：
- `Embedding`：第一层是图中最左边的7乘5的句子矩阵，每行是词向量，维度=5，这个可以类比为图像中的原始像素点。
- `Convolution`：然后经过 kernel_sizes=(2,3,4) 的一维卷积层，每个kernel_size 有两个输出 channel。
- `MaxPolling`：第三层是一个1-max pooling层，这样不同长度句子经过pooling层之后都能变成定长的表示。
- `FullConnection and Softmax`：最后接一层全连接的 softmax 层，输出每个类别的概率。

TextCNN的模型结构如下：

<img src="https://i.loli.net/2020/07/28/4zd5ngprFLqZoSO.png" alt="TextCNN" width="90%" />

```python
import logging

from keras import Input
from keras.layers import Conv1D, MaxPool1D, Dense, Flatten, concatenate, Embedding
from keras.models import Model
from keras.utils import plot_model

def textcnn(max_sequence_length, max_token_num, embedding_dim, output_dim, model_img_path=None, embedding_matrix=None):
    """ TextCNN: 1. embedding layers, 2.convolution layer, 3.max-pooling, 4.softmax layer. """
    x_input = Input(shape=(max_sequence_length,))
    logging.info("x_input.shape: %s" % str(x_input.shape))  # (?, 60)

    if embedding_matrix is None:
        x_emb = Embedding(input_dim=max_token_num, output_dim=embedding_dim, input_length=max_sequence_length)(x_input)
    else:
        x_emb = Embedding(input_dim=max_token_num, output_dim=embedding_dim, input_length=max_sequence_length,
                          weights=[embedding_matrix], trainable=True)(x_input)
    logging.info("x_emb.shape: %s" % str(x_emb.shape))  # (?, 60, 300)

    pool_output = []
    kernel_sizes = [2, 3, 4] 
    for kernel_size in kernel_sizes:
        c = Conv1D(filters=2, kernel_size=kernel_size, strides=1)(x_emb)
        p = MaxPool1D(pool_size=int(c.shape[1]))(c)
        pool_output.append(p)
        logging.info("kernel_size: %s \t c.shape: %s \t p.shape: %s" % (kernel_size, str(c.shape), str(p.shape)))
    pool_output = concatenate([p for p in pool_output])
    logging.info("pool_output.shape: %s" % str(pool_output.shape))  # (?, 1, 6)

    x_flatten = Flatten()(pool_output)  # (?, 6)
    y = Dense(output_dim, activation='softmax')(x_flatten)  # (?, 2)
    logging.info("y.shape: %s \n" % str(y.shape))

    model = Model([x_input], outputs=[y])
    if model_img_path:
        plot_model(model, to_file=model_img_path, show_shapes=True, show_layer_names=False)
    model.summary()
    return model
```

#### TextRNN

1. 结构1
流程：embedding—>BiLSTM—>concat final output/average all output----->softmax layer

<img src="https://i.loli.net/2020/07/28/vwRmjkPyd9n2rCc.png" alt="TextRNN1" width="60%" />

2. 结构2
流程：embedding–>BiLSTM---->(dropout)–>concat ouput—>UniLSTM—>(droput)–>softmax layer

<img src="https://i.loli.net/2020/07/28/kH46Et2SXnhgyKC.png" alt="TextRNN2" width="80%" />

[中文文本分类之TextRNN](https://www.cnblogs.com/Luv-GEM/p/10836454.html)

#### TextRNN+Attention

在textRNN的基础上，加入了attention机制，模型结构如下：

<img src="https://i.loli.net/2020/07/28/DO7XyQK9RcYx5oC.png" alt="TextRNN+Attention" width="60%" />

加入Attention之后最大的好处自然是能够直观的解释各个句子和词对分类类别的重要性。

#### TextRCNN

TextRCNN是TextRNN + CNN。结构如下：

<img src="https://i.loli.net/2020/07/28/QfMCjdWyJ9Ht1zc.png" alt="TextRCNN" width="80%" />

#### HAN

HAN是Hierarchical Attention Network的简称，结构如下：

<img src="https://i.loli.net/2020/07/28/4eENa93WGr7yoRz.png" alt="HAN1" width="60%" />

<img src="https://i.loli.net/2020/07/28/S8lkDgTtLbEsFvn.png" alt="HAN2" width="60%" />

模型结构分为了以下四个部分：
1. word encoder （BiGRU layer）
将每个单词转化为词向量，然后输入到双向的GRU网络中，获得该单词对应的隐藏输出
2. word attention （Attention layer）
根据重要性给单词赋予权重——定义一个随机上下文向量，计算其与句子中每个单词的相似度，然后经过一个softmax操作获得了一个归一化的attention权重矩阵
3. sentence encoder （BiGRU layer）
得到每个句子的向量表示，同理得到文档的向量表示
4. sentence attention （Attention layer）
将文档向量输入到softmax层进行分类

```python
class HAN(object):  
    def __init__(self, max_sentence_num, max_sentence_length, num_classes, vocab_size,  
                 embedding_size, learning_rate, decay_steps, decay_rate,  
                 hidden_size, l2_lambda, grad_clip, is_training=False,  
                 initializer=tf.random_normal_initializer(stddev=0.1)):  
        self.vocab_size = vocab_size  
        self.max_sentence_num = max_sentence_num  
        self.max_sentence_length = max_sentence_length  
        self.num_classes = num_classes  
        self.embedding_size = embedding_size  
        self.hidden_size = hidden_size  
        self.learning_rate = learning_rate  
        self.decay_rate = decay_rate  
        self.decay_steps = decay_steps  
        self.l2_lambda = l2_lambda  
        self.grad_clip = grad_clip  
        self.initializer = initializer  
        self.global_step = tf.Variable(0, trainable=False, name='global_step')  
        # placeholder  
        self.input_x = tf.placeholder(tf.int32, [None, max_sentence_num, max_sentence_length], name='input_x')  
        self.input_y = tf.placeholder(tf.int32, [None, num_classes], name='input_y')  
        self.dropout_keep_prob = tf.placeholder(tf.float32, name='dropout_keep_prob')  
        if not is_training:  
            return  
        word_embedding = self.word2vec()  
        sen_vec = self.sen2vec(word_embedding)  
        doc_vec = self.doc2vec(sen_vec)  
        self.logits = self.inference(doc_vec)  
        self.loss_val = self.loss(self.input_y, self.logits)  
        self.train_op = self.train()  
        self.prediction = tf.argmax(self.logits, axis=1, name='prediction')  
        self.pred_min = tf.reduce_min(self.prediction)  
        self.pred_max = tf.reduce_max(self.prediction)  
        self.pred_cnt = tf.bincount(tf.cast(self.prediction, dtype=tf.int32))  
        self.label_cnt = tf.bincount(tf.cast(tf.argmax(self.input_y, axis=1), dtype=tf.int32))  
        self.accuracy = self.accuracy(self.logits, self.input_y)
```

#### BERT

详见[Bert模型及其变种](https://iloveyou11.github.io/2020/07/16/Bert%E6%A8%A1%E5%9E%8B%E5%8F%8A%E5%85%B6%E5%8F%98%E7%A7%8D/)

[NLP之BERT中文文本分类超详细教程](https://blog.csdn.net/qq_20989105/article/details/89492442)

#### VDCNN

VDCNN是`Very Deep Convolutional Networks for Text Classiﬁcation`的缩写，是一个非常深度的卷积神经网络，论文中给出的实现有`9 layer，17 layer， 29 layer 以及49 layer`，真的是很非常深了。

VDCNN参照VGG和ResNet网络的特征，构建的网络具有如下特征:
1. Convolutional Block， 每个卷积块有两个卷积层，加上batchnorm、relu。卷积核尺寸size=3，池化步长pool_size=2；
2. 经过池化层pooling后，filters翻倍，也就是说，filters是成2^n层次增长的

VDCNN模型的结构如下：

<img src="https://i.loli.net/2020/07/28/pQKLzkWH7intaEU.png" alt="VDCNN" width="100%" />


使用keras搭建VDCNN模型实现的文本分类代码如下：[Keras-TextClassification](https://github.com/yongzhuo/Keras-TextClassification/tree/master/keras_textclassification/m08_TextVDCNN)，其中，VDCNN模型架构详见[graph.py](https://github.com/yongzhuo/Keras-TextClassification/blob/master/keras_textclassification/m08_TextVDCNN/graph.py)