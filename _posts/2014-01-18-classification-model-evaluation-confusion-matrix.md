---
title: 分类器评价与在R中的实现：混淆矩阵
author: c cm
layout: post
permalink: /classification-model-evaluation-confusion-matrix/
duoshuo_thread_id:
  - 1235579490248163341
post_views_count:
  - 0
categories:
  - R语言
  - 机器学习
tags:
  - 分类器
  - 模型评价
---
分类模型评价一般有以下几种方法：混淆矩阵(Confusion Matrix)、收益图(Gain Chart)、提升图(Lift Chart)、KS图(KS Chart)、接受者操作特性曲线(ROC Chart)。“分类模型评价与在R中的实现”系列中将逐个介绍。

本篇介绍最基础的混淆矩阵。

<!--more-->

# 一、混淆矩阵简介

混淆矩阵将分类预测结果与实际目标进行比较，并汇总成NXN列联表（N为分类类型数）。

以二元分类为例：

<table  style="width:100%; "  class="easy-table easy-table-default " border="0">
  <tr>
    <td  colspan="2">Confusion Matrix</td> <td >
    </td>
    
    <td colspan="2">
      Target
    </td>
    
    <td >
    </td>
  </tr>
  
  <tr>
    <td >
    </td>
    
    <td >
    </td>
    
    <td >
      Positive
    </td>
    
    <td >
      Negative
    </td>
  </tr>
  
  <tr>
    <td rowspan="2">
      Model
    </td>
    
    <td >
      Positive
    </td>
    
    <td >
      True Positives(TP)
    </td>
    
    <td >
      False Positives(FP)
    </td>
  </tr>
  
  <tr>
    <td >
      Negative
    </td>
    
    <td >
      False Negatives(FN)
    </td>
    
    <td >
      True Negatives(TN)
    </td>
  </tr>
  
  <tr>
    <td >
    </td>
    
    <td >
    </td>
    
    <td >
      Positive Samples(P)
    </td>
    
    <td >
      Negative Samples(N)
    </td>
  </tr>
</table>

由上表可以计算的指标有：  
Accuracy = (TP+TN)/(P+N)  
Error Rate = 1 &#8211; Accuracy = (FP+FN)/(P+N)  
False Positive Rate = Fallout = FP/N  
True Positive Rate = Recall = Sensitivity = TP/P  
False Negative Rate = Miss = FN/P  
True Negative Rate = Specificity = TN/N  
Positive Predictive Value = Precision = TP/(TP+FP)  
Negative Predictive Value = TN/(TN+FN)  
Prediction-conditioned Fallout = FP/(TP+FP)  
Prediction-conditioned Miss = FN/(TN+FN)  
Rate of Positive Predictions = Detection Prevalence = (TP+FP)/(P+N)  
Rate of Negative Predictions = (TN+FN)/(P+N)  
Prevalence = (TP+FN)/(P+N)  
Detection Rate = TP/(P+N)  
Balanced Accuracy = (Sensitivity+Specificity)/2

是不是感觉这些货已经组合完所有的分子/分母了？没关系，其实只要知道TP和TN越高越好就好了。

# 二、在R中计算混淆矩阵

这次使用ROCR包中的ROCR.simple数据集，其中prediction是预测值，labels为真实值。

<pre><code class="r">require(ROCR)
data(ROCR.simple)
str(ROCR.simple)
</code></pre>

    ## List of 2
    ##  $ predictions: num [1:200] 0.613 0.364 0.432 0.14 0.385 ...
    ##  $ labels     : num [1:200] 1 1 0 0 0 1 1 1 1 0 ...
    

## 1 用table()直接计算

在确定好阀值后，可以直接用table函数计算列联表，再根据之前的公式计算各个指标。假设我们认为prediciton>0.5的都预测为1，其余为0.

<pre><code class="r">pred.class &lt;- as.integer(ROCR.simple$predictions &gt; 0.5)
print(cft &lt;- table(pred.class, ROCR.simple$labels))
</code></pre>

    ##           
    ## pred.class  0  1
    ##          0 91 14
    ##          1 16 79
    

通过对混淆矩阵中数值的计算可以得到：

<pre><code class="r">tp &lt;- cft[2, 2]
tn &lt;- cft[1, 1]
fp &lt;- cft[2, 1]
fn &lt;- cft[1, 2]
print(accuracy &lt;- (tp + tn)/(tp + tn + fp + fn))
</code></pre>

    ## [1] 0.85
    

<pre><code class="r">print(sensitivity &lt;- tp/(tp + fn))
</code></pre>

    ## [1] 0.8495
    

<pre><code class="r">print(specificity &lt;- tn/(tn + fp))
</code></pre>

    ## [1] 0.8505
    

## 2 用confusionMatrix()算

如果不想手动算，可以借助caret包中的confusionMatrix函数计算。该函数既可以用混淆矩阵的结果，也可以直接输入预测/目标两列原始数据计算上述值。只要确定好positive分类是那个，就能得出跟之前一样的结果。

<pre><code class="r">require(caret)
confusionMatrix(cft, positive = "1")
</code></pre>

<pre><code class="r">confusionMatrix(pred.class, ROCR.simple$labels, positive = "1")
</code></pre>

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction  0  1
    ##          0 91 14
    ##          1 16 79
    ##                                         
    ##                Accuracy : 0.85          
    ##                  95% CI : (0.793, 0.896)
    ##     No Information Rate : 0.535         
    ##     P-Value [Acc > NIR] : <2e-16        
    ##                                         
    ##                   Kappa : 0.699         
    ##  Mcnemar's Test P-Value : 0.855         
    ##                                         
    ##             Sensitivity : 0.849         
    ##             Specificity : 0.850         
    ##          Pos Pred Value : 0.832         
    ##          Neg Pred Value : 0.867         
    ##              Prevalence : 0.465         
    ##          Detection Rate : 0.395         
    ##    Detection Prevalence : 0.475         
    ##       Balanced Accuracy : 0.850         
    ##                                         
    ##        'Positive' Class : 1             
    ## 
    

# 三、混淆矩阵的缺点

## 1 需要自己确定阀值

可以看到，混淆矩阵4个值的确定都依赖于最直线我们主观设定的0.5。如果只依靠混淆矩阵这种原始的方法，那么不经过繁琐的试错我们无法确认哪个阀值是最好的。

## 2不平衡数据鲁棒性不好

一些positive事件发生概率极小的不平衡数据集(imbalanced data),混淆矩阵可能效果不好。比如对信用卡交易是否异常做分类的情形，很可能1w笔交易中只有1笔交易是异常的。一个将将所有交易都判定为正常的分类器，准确率是99.99%。这个数字虽然很高，但是没有任何现实意义。

在之前列举的各种分类模型评价方法中，收益图(Gain Chart)、提升图(Lift Chart)、KS图(KS Chart)、接受者操作特性曲线(ROC Chart)都对混淆矩阵的缺点的有改进。参考后续。