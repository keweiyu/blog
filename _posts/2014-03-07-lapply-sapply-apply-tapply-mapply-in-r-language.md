---
title: R语言中的lapply sapply apply tapply mapply
author: c cm
layout: post
permalink: /lapply-sapply-apply-tapply-mapply-in-r-language/
duoshuo_thread_id:
  - 1235579490248163349
post_views_count:
  - 1
categories:
  - R语言
---
在R语言中，循环语句除了常见的for, while之外，还有很重要的apply族函数：lapply sapply apply tapply mapply。apply族函数是高效能计算的运算向量化(Vectorization)实现方法之一，比起传统的for,while常常能获得更好的性能。<!--more-->

# 一、apply族函数简介

lapply: apply族中最常用的一个，循环列表中的每一个元素，做相应运算，并返回与列表长度一致的结果。  
sapply: 可以理解为更友好版本的lapply。其返回结果会在lapply结果基础上进行相应简化：如果结果中元素的长度都是1，则返回向量；如果结果中元素的长度>1且相等，则返回矩阵；其他返回列表。  
apply: 对行或列进行运算，或更广泛地，对某几个维度进行边际运算。  
tapply: 接入参数INDEX，对数据分组进行运算，就和SQL中的by group一样。  
mapply: 支持传入两个以上的列表。

# 二、apply族函数例子

## 1 lapply和sapply

<div>
  <p>
    对这样一个列表，如果想要求其中各个元素的分位数，而equantile函数是不能直接运用于列表的。
  </p>
  
  <pre><code>x &lt;- list(a = 1:10, beta = exp(-3:3), logic = c(TRUE, FALSE, FALSE, TRUE))
quantile(x)
</code></pre>
  
  <pre><code>## Error: 'x' must be atomic
</code></pre>
  
  <p>
    此时可以用lapply，调用形式为lapply(X, FUN, &#8230;)，其中X为列表，FUN为需要apply的函数，&#8230;为函数的对应参数。
  </p>
  
  <p>
    例子中返回的结果是包含三个元素的列表，分别对应于输入的a, beta, logic。
  </p>
  
  <pre><code>lapply(x, quantile)
</code></pre>
  
  <pre><code>## $a
##    0%   25%   50%   75%  100% 
##  1.00  3.25  5.50  7.75 10.00 
## 
## $beta
##       0%      25%      50%      75%     100% 
##  0.04979  0.25161  1.00000  5.05367 20.08554 
## 
## $logic
##   0%  25%  50%  75% 100% 
##  0.0  0.0  0.5  1.0  1.0
</code></pre>
  
  <p>
    如果需要自定义quantile函数中的参数，可以在后方输入。像这样：
  </p>
  
  <pre><code>lapply(x, quantile, probs = c(1, 3)/4)
</code></pre>
  
  <pre><code>## $a
##  25%  75% 
## 3.25 7.75 
## 
## $beta
##    25%    75% 
## 0.2516 5.0537 
## 
## $logic
## 25% 75% 
##   0   1
</code></pre>
  
  <p>
    lapply的结果永远都会是和x长度相等的list,而sapply则不一样。所以对上面的结果（列表中元素的长度都是2），如果sapply会返回矩阵。
  </p>
  
  <pre><code>sapply(x, quantile, probs = c(1, 3)/4)
</code></pre>
  
  <pre><code>##        a   beta logic
## 25% 3.25 0.2516     0
## 75% 7.75 5.0537     1
</code></pre>
  
  <p>
    对于列表中元素长度都是1的，用sapply会返回向量。
  </p>
  
  <pre><code>sapply(x, mean)
</code></pre>
  
  <pre><code>##     a  beta logic 
## 5.500 4.535 0.500
</code></pre>
</div>

## 2 apply

apply函数形式为apply(X, MARGIN, FUN, &#8230;)，其中MARGIN = 1 表示行运算，= 2 表示列运算。3以上也是可以的。用apply可以很方便地按行列求和/平均，其结果与colMeans,colSums,rowMeans,rowSums是一样的。

    y <- matrix(rnorm(25), 5, 5)
    apply(y, 2, mean)
    

    ## [1] -0.10894  0.19696  0.15777  0.10310 -0.05672
    

    colMeans(y)
    

    ## [1] -0.10894  0.19696  0.15777  0.10310 -0.05672
    

    apply(y, 1, sum)
    

    ## [1]  0.49509 -1.57745  3.36263 -0.07178 -0.74765
    

    rowSums(y)
    

    ## [1]  0.49509 -1.57745  3.36263 -0.07178 -0.74765
    

在维度超过2（行和列）时,apply函数也可以进行运算。考虑根据影像数字识别这样的一个经典问题。其中一个简单的想法就是对同一数字所有影像的每个像素点上求平均，以此平均作为假设。简化这个问题如下例，如果是有100张图，3*3像素点，那么对每个点在100张图上的数字求平均过程如下。

    ys <- array(rnorm(900), c(3, 3, 100))
    apply(ys, c(1, 2), mean)
    

    ##          [,1]    [,2]     [,3]
    ## [1,]  0.10578  0.1730 -0.22176
    ## [2,] -0.07371 -0.1523  0.05377
    ## [3,]  0.07088 -0.1206  0.03520

## 3 mapply

对于一些接入变量2个及以上的函数，利用mapply可以简化使用多个for循环复杂的过程。比如，希望生成列表中第一个元素为含1个1的列表，第二个元素为含2个2的列表，以此类推。显然用mapply更省事。

    list(rep(1, 4), rep(2, 3), rep(3, 2), rep(4, 1))

    mapply(rep, 1:4, 4:1)
    

    ## [[1]]
    ## [1] 1 1 1 1
    ## 
    ## [[2]]
    ## [1] 2 2 2
    ## 
    ## [[3]]
    ## [1] 3 3
    ## 
    ## [[4]]
    ## [1] 4