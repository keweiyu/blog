---
title: 用信息值进行特征选择
author: c cm
layout: post
permalink: /feature-selection-using-information-value/
duoshuo_thread_id:
  - 1235579490248163330
views:
  - 0
post_views_count:
  - 0
categories:
  - 机器学习
tags:
  - 信息值
  - 特征选择
---
特征选择（feature selection）或者变量选择(variable selection)是在建模之前的重要一步。数据接口越来越多的今后，数据集的原始变量、衍生变量会越来越多，如何从中选取subset适用到模型之上在实际数据应用中十分重要。<!--more-->

信息值information value用来做特征选择最常用在计算信用评分卡时，是用来表示每一个变量对目标变量来说有多少“信息”的量。

对于一个分类变量性别，其计算过程如下：

<table border="0">
  <tr>
    <td>
      Group
    </td>
    
    <td>
      Good
    </td>
    
    <td>
      Bad
    </td>
    
    <td>
      pctlGood
    </td>
    
    <td>
      pctlBad
    </td>
    
    <td>
      WoE
    </td>
    
    <td>
      MIV
    </td>
  </tr>
  
  <tr>
    <td>
      女
    </td>
    
    <td>
      325
    </td>
    
    <td>
      19
    </td>
    
    <td>
      36.89%
    </td>
    
    <td>
      31.15%
    </td>
    
    <td>
      0.1692
    </td>
    
    <td>
      0.0097
    </td>
  </tr>
  
  <tr>
    <td>
      男
    </td>
    
    <td>
      556
    </td>
    
    <td>
      42
    </td>
    
    <td>
      63.11%
    </td>
    
    <td>
      68.85%
    </td>
    
    <td>
      -0.0871
    </td>
    
    <td>
      0.0050
    </td>
  </tr>
  
  <tr>
    <td>
    </td>
    
    <td>
    </td>
    
    <td>
    </td>
    
    <td>
    </td>
    
    <td>
    </td>
    
    <td>
      IV
    </td>
    
    <td>
      0.0147
    </td>
  </tr>
</table>

其中，

<img alt="WoE = ln(pctlGood/pctlBad)" src="http://latex.codecogs.com/gif.latex?WoE&space;=&space;ln(pctlGood/pctlBad)" align="absmiddle" />

<img alt="MIV = WoE * (pctlGood - pctlBad)" src="http://latex.codecogs.com/gif.latex?MIV&space;=&space;WoE&space;*&space;(pctlGood&space;-&space;pctlBad)" align="absmiddle" />

<img alt="IV = \sum MIV" src="http://latex.codecogs.com/gif.latex?IV&space;=&space;\sum&space;MIV" align="absmiddle" />

例中性别变量的信息值为0.0147，表示性别对目标变量的预测能力非常弱。

一般说来，信息值0.02以下表示与目标变量相关性非常弱。0.02-0.1很弱；0.1-0.3一般；0.3-0.5强；0.5-1很强。

使用信息值最大的优点是简单快速。缺点包括：对于数值型变量需要分类预处理；无法确定iv值在多少以上便保留变量；无法识别相关性强的变量，可能同时保留造成冗余；无法识别变量间关系。

Reference:

<a href="http://www.sas.com/resources/whitepaper/wp_10961.pdf" target="_blank">Building Credit Scorecards Using Credit Scoring for SAS</a>  
<a href="http://plug-n-score.com/learning/characteristics-selection-using-information-value.htm" target="_blank">Characteristics Selection Using Information Value</a>