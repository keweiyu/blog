---
title: 机器学习入门-机器学习的不同类型
author: c cm
layout: post
permalink: /types-of-machine-learning/
duoshuo_thread_id:
  - 1235579490248163335
views:
  - 0
post_views_count:
  - 0
categories:
  - 机器学习
tags:
  - 主动学习
  - 分类器
  - 半监督式学习
  - 回归问题
  - 增强式学习
  - 批次学习
  - 监督式学习
  - 线上学习
  - 结构化预测
  - 非监督式学习
---
机器学习指的是机器通过学习数据，得到技巧的过程。比如，现在需要让机器学习病人判断是否获病。我们可以从目标整体(population)所有人中抽出用以训练的样本(sample)，把这些样本的病人、正常人身体资料作为输入(input)，是否患病等讯息作为输出(output)喂给机器，机器以某种学习算法(training algorithm)对其进行处理，最后获得某种有用的知识(final hypothesis)，比如某种根据N个变量的关系判断是否患病的计算公式。我们希望用有效的算法使得这个最终的hypothesis尽量贴近整体的真实情况。根据这个过程中的几个关键字，可以将机器学习分类成不同类型。

按照输入空间不同：特征学习(feature learning)  
按照输出空间不同：二元分类(binary classification)，多元分类(multiclass classification)，回归问题(regression)，结构化预测(structured prediction)  
按照输出标记类型不同：监督式学习(supervised learning)，非监督式学习(unsupervised learning)，半监督式学习(semi-supervised learning)，增强式学习(reinforcement learning)。这也是机器学习中最常见的分类  
按照学习方法不同：批次学习（Batch Learning），线上学习（Online Learning），主动学习(Active Learning)。  
<!--more-->

# 一、特征学习(feature learning)

机器学习中的信息来源——数据变量，可能是以不同的方式提供的。  
如果提供的变量，是非常具体的，可以直接使用，比如对判断病人是否得病例子中，提供病人的身高体重性别等身体常规指标，及一些化验结果指标。这样的变量都叫做concrete features，一般由建模者选择出来使用。  
相应的，有concrete feature，就有raw/abstract feature。这些征是非常抽象的，需要经过一定地加工，提取特征才能使用，提取特征的过程就叫做特征学习(<a href="http://en.wikipedia.org/wiki/Feature_learning" target="_blank">feature learning</a>)，或者特征工程(feature engineering)。比如在辨识手写数字图片时，我们很少会将这些n*m格的像素各格灰度直接用作变量，输入模型中。我们可能会先对图片通过人脑经验做些处理，比如提取出左右/上下对称，总笔画浓度等等一些特征；或者借由机器学习的算法提取特征，<a href="http://yann.lecun.com/exdb/lenet/" target="_blank">eg</a>。最近很火的深度学习(deep learning)最简单的说，也是用非监督方式抽取具体特征的一种算法。

# 二、二元分类、多元分类、回归分析、结构化预测

二元分类（<a href="http://en.wikipedia.org/wiki/Binary_classification" target="_blank">binary classification</a>），目标变量取值只有两种可能性，简单地说就是做判断题，在现实生活中应用非常广泛。银行根据客户资料，判断信用好坏以决定是否发放信用卡/贷款（<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29" target="_blank">german credit data</a>)；根据邮件内容，判断是否垃圾邮件(<a href="http://csmining.org/index.php/spam-email-datasets-.html" target="_blank">spam email</a>)；根据病人资料，判断病人是否患癌症(<a href="http://archive.ics.uci.edu/ml/datasets/Breast+Cancer" target="_blank">breast cancer</a>)等等。二元分类是机器学习中最基本最核心的问题，许多其他演算法的基础都来自他。

多元分类（<a href="http://en.wikipedia.org/wiki/Multiclass_classification" target="_blank">Multiclass Classification</a>），简单地说就是做选择题，目标变量是level>1的factor。比如判断某种植物所属科目（<a href="http://archive.ics.uci.edu/ml/datasets/Iris" target="_blank">iris</a>)，评估汽车价值高低(<a href="http://archive.ics.uci.edu/ml/datasets/Car+Evaluation" target="_blank">car evaluation</a>)等等。

回归分析（<a href="http://en.wikipedia.org/wiki/Regression_analysis" target="_blank">regression analysis</a>)的目标变量是Numeric类型的，取值无限多个点。典型的回归分析问题包括用历史数据做出历年GDP的回归线，用相关变量估计房屋价格(<a href="http://archive.ics.uci.edu/ml/datasets/Housing" target="_blank">Housing Data Set</a>)等等。

结构化预测(<a href="http://en.wikipedia.org/wiki/Structured_prediction" target="_blank">Structured Learning</a>)的目标变量和以上三者都不一样，是一种结构。典型的结构化学习是对一个语言中的分析树(parse tree)。

# 三、监督式学习、非监督式学习、半监督式学习、强化学习

<span style="line-height: 1.5;">监督式学习（<a href="http://en.wikipedia.org/wiki/Supervised_learning" target="_blank">Supervised Learning</a>）中所有输入，都有输出标签。</span>

非监督式学习（<a href="http://en.wikipedia.org/wiki/Unsupervised_learning" target="_blank">Unsupervised Learning</a>）中没有对应于每个观测的标签。典型的问题是聚类问题(<a href="http://en.wikipedia.org/wiki/Cluster_analysis" target="_blank">Clustering Analysis</a>)比如对客户分群，我们不知道总共有几个客户类型，更不知道每个客户是属于哪个类型。但是可以根据客户资料将一些属性相似的客户分成一群。

其他非监督式学习还包括密度分析问题(density estimation)比如将犯罪案发比较多的地区标注出来。异常检测(anomaly detection)比如在visa交易过程中将异常情况发现出来。

对监督式学习来说，最后的假说能使预测值越贴近输出标签越好。但是对于非监督式学习就没有那么明确的判断标准了。

半监督式学习(<a href="http://en.wikipedia.org/wiki/Semi-supervised_learning" target="_blank">semi-supervised</a>)：是监督式学习和非监督式学习的综合，不是所有案例都有标注类型。如果是对input取得很简单，但是对如果取得所有output tag很难或很贵，那么可以采取半监督式学习的方式。

强化学习（<a href="http://en.wikipedia.org/wiki/Reinforcement_learning" target="_blank">Reinforcement Learning</a>）的输出标签不是直接的对/不对，而是一种奖惩机制。举例来说，训练宠物的时候无法直接告诉它，做出某个手势时想要它干嘛，或者它的反应是对是错。但是可以通过做对了奖励吃的，做错了就凶它的奖惩方法训练。虽然它还是无法和人直接沟通，不明白手势含义，但是渐渐使宠物能对手势做出正确反应。

# 四、批次学习、线上学习、主动学习

批次学习(Batch Learning) 把所有资料都直接用于机器学习，比如用所有的点按照OLS拟合出回归线。这是最常见的机器学习方式。  
线上学习(<a href="http://en.wikipedia.org/wiki/Online_machine_learning" target="_blank">Online Learning</a>)中的的online并不是指需要上网，而是指对资料的运用增加一步“更新”的工作。比如用一些图像管理的软件可以帮你识别人脸。软件会一个个问你该面孔对应名字。机器学习到的东西是在一轮轮问题中更新的，每次学习一个观测。之前提到的“增强式学习”异曲同工。  
主动学习(<a href="http://en.wikipedia.org/wiki/Active_learning_(machine_learning)" target="_blank">Active Learning</a>)比起线上学习更具主动性。主动学习能交互地询问使用者问题，并将获得的结果当做新的数据点。拿之前图像管理的例子来说，主动学习可能会通过算法做出某种图像询问你，该人对应的名字。

机器学习包括的大概类型主要是以上几种了，更多可以参考<a href="http://en.wikipedia.org/wiki/Machine_learning" target="_blank">wiki</a>。如有疏漏请留言。:)