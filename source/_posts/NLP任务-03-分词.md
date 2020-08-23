---
title: NLP任务-03-分词
date: 2020-06-13
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



### 分词
分词算法有以下几种：
1. 最大匹配算法（贪心算法）
包括前向最大匹配和后向最大匹配，缺点是不能考虑语义，可能是局部最优
2. 考虑语义
基本思想：根据输入的句子，生成所有可能的分割，根据LM（语言模型，用来评估句子的合理程度，后面会讲到）选择其中最好的分割方式，但是效率低下。
3. 维特比算法
根据词典概率，构建连接图，使用DP动态规划

<img width="70%" src="https://i.loli.net/2020/03/24/wEi7GCpbOzsnx38.jpg" alt="维特比算法" />

核心问题变为：如何找到最短的路径

**总结：**
1. 基于匹配规则的方法（max-matching）
2. 基于概率的方法（LM,HMM,CRF）
3. 分词可以认为是已经解决的问题，基本不会涉及到重新设计分词，可能会根据业务需求有小的修改

### 实战
0. 加载词典
[CoreNatureDictionary.mini.txt](https://github.com/NLP-LOVE/Introduction-NLP/blob/master/data/dictionnary/CoreNatureDictionary.mini.txt)

```python
def load_dictionary():
    dic = set()

    # 按行读取字典文件，每行第一个空格之前的字符串提取出来。
    for line in open("CoreNatureDictionary.mini.txt","r"):
        dic.add(line[0:line.find('  ')])
    
    return dic
```
1. 完全切分
```python
def fully_segment(text, dic):
    word_list = []
    for i in range(len(text)):                  # i 从 0 到text的最后一个字的下标遍历
        for j in range(i + 1, len(text) + 1):   # j 遍历[i + 1, len(text)]区间
            word = text[i:j]                    # 取出连续区间[i, j]对应的字符串
            if word in dic:                     # 如果在词典中，则认为是一个词
                word_list.append(word)
    return word_list
  
dic = load_dictionary()
print(fully_segment('就读北京大学', dic))
```
2. 正向最长匹配
```python
def forward_segment(text, dic):
    word_list = []
    i = 0
    while i < len(text):
        longest_word = text[i]                      # 当前扫描位置的单字
        for j in range(i + 1, len(text) + 1):       # 所有可能的结尾
            word = text[i:j]                        # 从当前位置到结尾的连续字符串
            if word in dic:                         # 在词典中
                if len(word) > len(longest_word):   # 并且更长
                    longest_word = word             # 则更优先输出
        word_list.append(longest_word)              # 输出最长词
        i += len(longest_word)                      # 正向扫描
    return word_list

dic = load_dictionary()
print(forward_segment('就读北京大学', dic))
print(forward_segment('研究生命起源', dic))
```
3. 逆向最长匹配
```python
def backward_segment(text, dic):
    word_list = []
    i = len(text) - 1
    while i >= 0:                                   # 扫描位置作为终点
        longest_word = text[i]                      # 扫描位置的单字
        for j in range(0, i):                       # 遍历[0, i]区间作为待查询词语的起点
            word = text[j: i + 1]                   # 取出[j, i]区间作为待查询单词
            if word in dic:
                if len(word) > len(longest_word):   # 越长优先级越高
                    longest_word = word
                    break
        word_list.insert(0, longest_word)           # 逆向扫描，所以越先查出的单词在位置上越靠后
        i -= len(longest_word)
    return word_list

dic = load_dictionary()
print(backward_segment('研究生命起源', dic))
print(backward_segment('项目的研究', dic))
```
4. 双向最长匹配
同时执行正向和逆向最长匹配，若两者的词数不同，则返回词数更少的那一个。否则，返回两者中单字更少的那一个。当单字数也相同时，优先返回逆向最长匹配的结果。
```python
def count_single_char(word_list: list):  # 统计单字成词的个数
    return sum(1 for word in word_list if len(word) == 1)


def bidirectional_segment(text, dic):
    f = forward_segment(text, dic)
    b = backward_segment(text, dic)
    if len(f) < len(b):                                  # 词数更少优先级更高
        return f
    elif len(f) > len(b):
        return b
    else:
        if count_single_char(f) < count_single_char(b):  # 单字更少优先级更高
            return f
        else:
            return b                                     # 都相等时逆向匹配优先级更高
        

print(bidirectional_segment('研究生命起源', dic))
print(bidirectional_segment('项目的研究', dic))
```
5. 使用HanLP实现分词
```python
from pyhanlp import *

# 不显示词性
HanLP.Config.ShowTermNature = False

# 可传入自定义字典 [dir1, dir2]
segment = DoubleArrayTrieSegment()
# 激活数字和英文识别
segment.enablePartOfSpeechTagging(True)

print(segment.seg("江西鄱阳湖干枯，中国最大淡水湖变成大草原"))
print(segment.seg("上海市虹口区大连西路550号SISU"))
```
去掉停用词：
[stopwords.txt](https://github.com/NLP-LOVE/Introduction-NLP/blob/master/data/dictionnary/stopwords.txt)
```python
def load_from_file(path):
    """
    从词典文件加载DoubleArrayTrie
    :param path: 词典路径
    :return: 双数组trie树
    """
    map = JClass('java.util.TreeMap')()  # 创建TreeMap实例
    with open(path) as src:
        for word in src:
            word = word.strip()  # 去掉Python读入的\n
            map[word] = word
    return JClass('com.hankcs.hanlp.collection.trie.DoubleArrayTrie')(map)

## 去掉停用词
def remove_stopwords_termlist(termlist, trie):
    return [term.word for term in termlist if not trie.containsKey(term.word)]

trie = load_from_file('stopwords.txt')
termlist = segment.seg("江西鄱阳湖干枯了，中国最大的淡水湖变成了大草原")
print('去掉停用词前：', termlist)
print('去掉停用词后：', remove_stopwords_termlist(termlist, trie))
```