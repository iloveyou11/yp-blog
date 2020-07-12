---
title: transformer
date: 2020-07-12
categories: AI
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

### 什么是transformer
一句话解释：transformer是带有self-attention的seq2seq，输出能同时计算，可以代替RNN结构（必须按输入的顺序计算），因此transformer对于sequence to sequence的应用场景更为高效。

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

<img src="https://i.loli.net/2020/07/12/2N6PSW1F5ORb4zD.png" width="50%" alt="transformer计算流程1" />

第2步：对每个αi计算softmax值

<img src="https://i.loli.net/2020/07/12/LvVNyuGleXH84ba.png" width="50%" alt="transformer计算流程2" />

第3步：使用右上角的公式，计算每个vector对应的输出b

<img src="https://i.loli.net/2020/07/12/NLS2CcavgdDGwUA.png" width="50%" alt="transformer计算流程3" />

【注意】以上从x1到xn的运算是可以同时进行的，通过attention值的大小，可以与其他的vector产生上下文关联。

### transformer为何计算速度快
我们可以将以上的计算过程使用一连串的矩阵乘法解决，而矩阵乘法可以轻易地使用GPU来加速。如下图所示：

<img src="https://i.loli.net/2020/07/12/cV5HT1NZDBjloUf.png" width="50%" alt="transformer矩阵计算1" />

<img src="https://i.loli.net/2020/07/12/HkTUR4qDAmXW3C8.png" width="50%" alt="transformer矩阵计算2" />

<img src="https://i.loli.net/2020/07/12/KWYHyGko3eEFTmB.png" width="50%" alt="transformer矩阵计算3" />

<img src="https://i.loli.net/2020/07/12/ryFezKHq2cv5TJl.png" width="50%" alt="transformer矩阵计算4" />

<img src="https://i.loli.net/2020/07/12/oM3ZXHFuBzD67Wn.png" width="50%" alt="transformer矩阵计算5" />

<img src="https://i.loli.net/2020/07/12/UzZWQCG64Et9y5P.png" width="50%" alt="transformer矩阵计算6" />

具体解析详见[NLP-06](https://iloveyou11.github.io/2020/04/05/NLP-06/)中的self-attention内容。