---
title: NLP任务-04-词性标注
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

不同的语料库采用了不同的词性标注集，一般都含有形容词、动词、名词等常见词性。

<img src="https://i.loli.net/2020/07/29/pUrzawD8XRI2LJP.png" alt="词性标注" width="80%" />

词性标注的作用：
1. 下游遇到OOV时，根据词性可以猜测用法
2. 可以抽取信息，如描述一件物品的所有形容词
3. ……

`词性标注`指的是为句子中每个单词预测一个词性标签的任务。它具有有以下两个难点:
1. 汉语中一个单词多个词性的现象很常见，但在具体语境下一定是唯一词性。
2. OOV 是任何自然语言处理任务的难题。

`词性标注模型`可以将中文分词中的汉字替换为词语，{B,M,E,S} 替换为“名词、动词、形容词等”，序列标注模型马上就可以用来做词性标注。

### 基于HMM的词性标注

[代码](https://github.com/NLP-LOVE/Introduction-NLP/tree/master/code/ch07/hmm_pos.py)

```python
from  pyhanlp import *
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


## 指定 PKU 语料库
PKU98 = ensure_data("pku98", "http://file.hankcs.com/corpus/pku98.zip")
PKU199801 = os.path.join(PKU98, '199801.txt')
PKU199801_TRAIN = os.path.join(PKU98, '199801-train.txt')
PKU199801_TEST = os.path.join(PKU98, '199801-test.txt')
POS_MODEL = os.path.join(PKU98, 'pos.bin')
NER_MODEL = os.path.join(PKU98, 'ner.bin')


## ===============================================
## 以下开始 HMM 词性标注



HMMPOSTagger = JClass('com.hankcs.hanlp.model.hmm.HMMPOSTagger')
AbstractLexicalAnalyzer = JClass('com.hankcs.hanlp.tokenizer.lexical.AbstractLexicalAnalyzer')
PerceptronSegmenter = JClass('com.hankcs.hanlp.model.perceptron.PerceptronSegmenter')
FirstOrderHiddenMarkovModel = JClass('com.hankcs.hanlp.model.hmm.FirstOrderHiddenMarkovModel')
SecondOrderHiddenMarkovModel = JClass('com.hankcs.hanlp.model.hmm.SecondOrderHiddenMarkovModel')

def train_hmm_pos(corpus, model):
    tagger = HMMPOSTagger(model)  # 创建词性标注器
    tagger.train(corpus)  # 训练
    print(', '.join(tagger.tag("他", "的", "希望", "是", "希望", "上学")))  # 预测
    analyzer = AbstractLexicalAnalyzer(PerceptronSegmenter(), tagger)  # 构造词法分析器，与感知机分词器结合，能同时进行分词和词性标注。
    print(analyzer.analyze("他的希望是希望上学"))  # 分词+词性标注
    print(analyzer.analyze("他的希望是希望上学").translateLabels())  # 对词性进行翻译
    print(analyzer.analyze("李狗蛋的希望是希望上学").translateLabels())  # 对词性进行翻译
    return tagger


if __name__ == '__main__':
    print('一阶隐马尔可夫模型:')
    tagger1 = train_hmm_pos(PKU199801_TRAIN, FirstOrderHiddenMarkovModel())   # 一阶隐马
    print('')
    print('二阶隐马尔可夫模型:')
    tagger = train_hmm_pos(PKU199801_TRAIN, SecondOrderHiddenMarkovModel())  # 二阶隐马
```

运行结果：
```
一阶隐马尔可夫模型:
r, u, n, v, v, v
他/r 的/u 希望/n 是/v 希望/v 上学/v
他/代词 的/助词 希望/名词 是/动词 希望/动词 上学/动词
李狗蛋/动词 的/动词 希望/动词 是/动词 希望/动词 上学/动词

二阶隐马尔可夫模型:
r, u, n, v, v, v
他/r 的/u 希望/n 是/v 希望/v 上学/v
他/代词 的/助词 希望/名词 是/动词 希望/动词 上学/动词
李狗蛋/动词 的/动词 希望/动词 是/动词 希望/动词 上学/动词
```

但是HMM模型进行词性标注存在OOV问题，它一步走错则会导致更多的错误，HMM模型只利用单词这特征，无法通过姓氏去预测人名。

### 基于感知机的词性标注

[代码](https://github.com/NLP-LOVE/Introduction-NLP/tree/master/code/ch07/perceptron_pos.py)

```python
from  pyhanlp import *
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

## 指定 PKU 语料库
PKU98 = ensure_data("pku98", "http://file.hankcs.com/corpus/pku98.zip")
PKU199801 = os.path.join(PKU98, '199801.txt')
PKU199801_TRAIN = os.path.join(PKU98, '199801-train.txt')
PKU199801_TEST = os.path.join(PKU98, '199801-test.txt')
POS_MODEL = os.path.join(PKU98, 'pos.bin')
NER_MODEL = os.path.join(PKU98, 'ner.bin')

## ===============================================
## 以下开始 感知机 词性标注

AbstractLexicalAnalyzer = JClass('com.hankcs.hanlp.tokenizer.lexical.AbstractLexicalAnalyzer')
PerceptronSegmenter = JClass('com.hankcs.hanlp.model.perceptron.PerceptronSegmenter')
POSTrainer = JClass('com.hankcs.hanlp.model.perceptron.POSTrainer')
PerceptronPOSTagger = JClass('com.hankcs.hanlp.model.perceptron.PerceptronPOSTagger')

def train_perceptron_pos(corpus):
    trainer = POSTrainer()
    trainer.train(corpus, POS_MODEL)  # 训练感知机模型
    tagger = PerceptronPOSTagger(POS_MODEL)  # 加载
    analyzer = AbstractLexicalAnalyzer(PerceptronSegmenter(), tagger)  # 构造词法分析器，与感知机分词器结合，能同时进行分词和词性标注。
    print(analyzer.analyze("李狗蛋的希望是希望上学"))  # 分词+词性标注
    print(analyzer.analyze("李狗蛋的希望是希望上学").translateLabels())  # 对词性进行翻译
    return tagger

if __name__ == '__main__':
    train_perceptron_pos(PKU199801_TRAIN)
```

运行结果：
```
李狗蛋/nr 的/u 希望/n 是/v 希望/v 上学/v
李狗蛋/人名 的/助词 希望/名词 是/动词 希望/动词 上学/动词
```

可见，这次的运行结果是完全正确的，可以正确的识别出OOV。

### 基于CRF的词性标注
[代码](https://github.com/NLP-LOVE/Introduction-NLP/tree/master/code/ch07/crf_pos.py)

```python
from  pyhanlp import *
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

## 指定 PKU 语料库
PKU98 = ensure_data("pku98", "http://file.hankcs.com/corpus/pku98.zip")
PKU199801 = os.path.join(PKU98, '199801.txt')
PKU199801_TRAIN = os.path.join(PKU98, '199801-train.txt')
PKU199801_TEST = os.path.join(PKU98, '199801-test.txt')
POS_MODEL = os.path.join(PKU98, 'pos.bin')
NER_MODEL = os.path.join(PKU98, 'ner.bin')

## ===============================================
## 以下开始 CRF 词性标注

AbstractLexicalAnalyzer = JClass('com.hankcs.hanlp.tokenizer.lexical.AbstractLexicalAnalyzer')
PerceptronSegmenter = JClass('com.hankcs.hanlp.model.perceptron.PerceptronSegmenter')
CRFPOSTagger = JClass('com.hankcs.hanlp.model.crf.CRFPOSTagger')

def train_crf_pos(corpus):
    # 选项1.使用HanLP的Java API训练，慢
    tagger = CRFPOSTagger(None)  # 创建空白标注器
    tagger.train(corpus, POS_MODEL)  # 训练
    tagger = CRFPOSTagger(POS_MODEL) # 加载
    # 选项2.使用CRF++训练，HanLP加载。（训练命令由选项1给出）
    # tagger = CRFPOSTagger(POS_MODEL + ".txt")
    analyzer = AbstractLexicalAnalyzer(PerceptronSegmenter(), tagger)  # 构造词法分析器，与感知机分词器结合，能同时进行分词和词性标注。
    print(analyzer.analyze("李狗蛋的希望是希望上学"))  # 分词+词性标注
    print(analyzer.analyze("李狗蛋的希望是希望上学").translateLabels())  # 对词性进行翻译
    return tagger

if __name__ == '__main__':
    tagger = train_crf_pos(PKU199801_TRAIN)
```

运行结果：
```
李狗蛋/nr 的/u 希望/n 是/v 希望/v 上学/v
李狗蛋/人名 的/助词 希望/名词 是/动词 希望/动词 上学/动词
```

可见，这次的运行结果是完全正确的，依然可以正确的识别出OOV。

### 算法对比

结构化感知机和条件随机场都要优于隐马尔可夫模型，判别式模型能够利用更多的特征来进行训练，从而提高更多的精度。