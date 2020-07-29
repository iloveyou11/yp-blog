---
title: NLP任务-06-依存语法分析
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

`语法分析`是分析句子的语法结构，将其转化为容易理解的结构（往往是树形结构）。

推荐阅读[依存语法分析](https://github.com/NLP-LOVE/Introduction-NLP/blob/master/chapter/12.%E4%BE%9D%E5%AD%98%E5%8F%A5%E6%B3%95%E5%88%86%E6%9E%90.md)