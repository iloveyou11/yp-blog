---
title: NLP系列3：语言系统与NLP基础
date: 2020-02-27
categories: NLP
author: yangpei
comments: true
cover_picture: /images/banner.jpg
---

核心内容：专家系统、基于概率的系统、正则项、正则项的运用、正则参数搜索策略、MLE/MAP、lasso、set-coverage、SVM

<!-- more -->




#### 专家系统
**专家系统=推理引擎+知识**，利用知识和推理来解决决策问题
专家系统与基于概率的系统的区别：
1. 专家系统适合于没有数据源或数据源很少的情况，根据专家的经验拟定一条条规则进行匹配
```
if condition1:
	do something1
else if condition12
	do something2
else if ...
```
2. 基于概率的系统是根据给定的数据D={X,Y}，学习x到y的映射关系，通常适用于有大量数据源的情况

基本流程如下：
<img width="70%" src="https://i.loli.net/2020/03/24/l2oDvsh93ZQbF7G.jpg" alt="专家系统" />

#### 基于概率的系统
使用机器学习方法解决问题

**监督学习常用方法：**
- 线性回归
- 逻辑回归
- 朴素贝叶斯
- 神经网络
- SVM
- 随机森林
- Adaboost
- CNN

**非监督学习常用方法：**
- k-means（聚类）
- PCA（主成分分析）
- ICA
- MF（矩阵分解）
- LSA（latent semantic analysis）
- LDA（latent Dirichlet analysis）

#### 正则项
- L0：一般不用
- L1：sparse参数，会将一些参数变为0，稀疏参数（相当于激活部分神经元）
- L2：减小参数，但不会减为0

关于模型：
- 好的模型拥有高的泛化能力
- 越复杂的模型越容易过拟合
- 添加正则项是防止过拟合的一种手段，其他还有dropout、early-stopping等等
- L1正则会带来稀疏特性
- 选择超参数时使用交叉验证
- 参数搜索过程最耗费资源

#### 关于正则项的灵活应用
1. 场景：模拟大脑（某一个区域只有少部分神经元被激活，在空间上相邻的神经元作用类似）

<img width="60%" src="https://i.loli.net/2020/03/24/4aM9FzKUIkf2Qyq.jpg" alt="正则场景1" />

2. 场景：动态推荐系统（U/V矩阵参数不能过大，而且相近时间用户特征和商品特征不会改变太大）

<img width="60%" src="https://i.loli.net/2020/03/24/NQPwGg37RjSTEA4.jpg" alt="正则场景2" />

#### 参数搜索策略
如何选择正则项中的超参数`λ`呢？
0. grid search

<img width="50%" src="https://i.loli.net/2020/03/24/LrgJGIixyaWVPE5.jpg" alt="grid search" />

1. 随机搜索
λ1区间[a,b]，λ2区间[c,d]，随机抽取值
2. 遗传算法
尽量往好的方向去搜索，抛弃不太好的区间（好的个体更倾向于产生的个体）。
尝试λ效果不错的话，不断产生子枝，生成与原来λ有关的新λ，池虚繁殖后代，如果表现不好就直接舍弃
3. 贝叶斯优化
不断调整先验，使后验概率不断变化，和遗传算法核心思想类似，但是采用的手段不同

#### MLE/MAP
**MLE：**最大似然估计（仅仅考虑数据本身）
**MAP：**最大后验估计（需要考虑先验知识）

MLE与MAP的直观理解：
1、MLE（最大似然估计）：扔硬币，仅仅通过观测值2/3来预测正/反面向上的概率
2、MAP（最大后验估计）：扔硬币，仍然会得到观测值2/3，但拥有这个硬币的主人告诉我，出现正面的概率大概是80%，会与我的观测值2/3出现偏差（可能我观测到的样本不够多），这时计算正/反面向上的概率应结合先验概率（主人的想法）为66.7%~80%之间

随着样本数杨的增大，先验所起到的作用会越来越小。当样本数N趋近于无穷大时，MAP的解趋近于MLE的解。

`adding prior is equivalent to regularization！`
`Gaussian Prior is L2 regularization`——高斯先验相当于加了L2正则项

<img src="https://i.loli.net/2020/07/17/wxysWkM3ESdbqKY.png" alt="L2" width="80%" />

`Laplace Prior is L1 regularization`——拉普拉斯先验相当于加了L1正则项

<img src="https://i.loli.net/2020/07/17/Z1ncrgqLazU5sHM.png" alt="L1" width="80%" />

#### lasso
**特征选择策略：**
1. exhausted search：考虑所有可能的特征组合，评估各自准确率
2. greedy approach：贪心算法，前向或后向选择特征，计算组合特征的准确率
3. via regularization：lasso算法，通过目标函数的方法构建特征组合，包括L1、L2正则

**为什么偏向于稀疏特征？**
1. 如果维度太高，计算量也变得很高
2. 在稀疏性条件下，计算量只依赖于非0项的个数
3. 提高可解释性

使用L1正则化稀疏变量，但是存在导数不存在的点，使用SGD不容易进行梯度下降。L1正则的导数不是连续的，需要使用`coordinate descent`进行梯度下降：核心思想是，先固定其他w，只调整一个维度的w寻找最优解，依次找到各个维度w的最优解。lasso objective一定会收敛。

怎么选择下一个coordinate ？
1. 依次选择dimension
2. random select


#### set-coverage problem
问题描述：

<img width="60%" src="https://i.loli.net/2020/03/24/FiHomXwWTuxqYfd.jpg" alt="set-coverage" />

方法：
1. 穷举（最笨的方法，但是可以求解）
2. 贪心算法（并不能保证找到的就是最优解）
3. 最优化

<img width="100%" src="https://i.loli.net/2020/03/24/7ZxLw4ldUJKfchr.jpg" alt="set-coverage-solution" />

#### SVM

<img style="display:block" width="40%" src="https://i.loli.net/2020/03/24/4w97WlatRuHb85G.jpg" alt="SVM" />

<img width="40%" src="https://i.loli.net/2020/03/24/gDfd3BVZvr68kQX.jpg" alt="SVM目标函数" />

- 什么是KKT条件？（等式条件、不等式条件）
我们之前学习过，针对满足等式条件的最优解问题，采用Lagrange乘子法解决，而需要满足不等式条件或不等式、等式组合条件的称为KKT条件

- 什么是Primal-Dual Problem？
[优化方法：原问题和拉格朗日对偶问题（primal-dual）](https://blog.csdn.net/zuzhiang/article/details/103293545)

- 什么是kernal trick？
将原来的特征映射到更高维度的空间中，使其更容易分类，但是计算量不会随之增加。