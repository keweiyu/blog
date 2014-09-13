---
title: R语言中生成虚拟变量/哑变量
author: c cm
layout: post
permalink: /generate-dummy-variable-in-r-language/
duoshuo_thread_id:
  - 1235579490248163336
views:
  - 0
post_views_count:
  - 0
categories:
  - R语言
tags:
  - 神经网络
---
在R语言中对包括分类变量(factor)的数据建模时，一般会将其自动处理为虚拟变量或哑变量(dummy variable)。但有一些特殊的函数，如neuralnet包中的neuralnet函数就不会预处理。如果直接将原始数据扔进去，会出现&#8221;requires numeric/complex matrix/vector arguments&#8221;需要数值/复数矩阵/矢量参数错误。

这个时候，除了将这些变量删除，我们只能手动将factor variable转换为取值(0,1)的虚拟变量。所用的函数一般有model.matrix(),nnet package中的class.ind()。<!--more-->

下面以UCI的german credit data为例说明。

首先，从UCI网站上下载到german.data数据集，并用str函数对其有个简单的认识。

    download.file("http://archive.ics.uci.edu/ml/machine-learning-databases/statlog/german/german.data", 
        "./german.data")
    data <- read.table("./german.data")
    str(data)
    

该数据有21个变量，其中V21为目标变量，V1-V20中包括integer和factor两种类型。下面将用V1分类变量（包含4个level）和V2,V5,V8三个数值型变量作为解释变量建模。

    ## 'data.frame':    1000 obs. of  21 variables:
    ##  $ V1 : Factor w/ 4 levels "A11","A12","A13",..: 1 2 4 1 1 4 4 2 4 2 ...
    ##  $ V2 : int  6 48 12 42 24 36 24 36 12 30 ...
    ##  $ V3 : Factor w/ 5 levels "A30","A31","A32",..: 5 3 5 3 4 3 3 3 3 5 ...
    ##  $ V4 : Factor w/ 10 levels "A40","A41","A410",..: 5 5 8 4 1 8 4 2 5 1 ...
    ##  $ V5 : int  1169 5951 2096 7882 4870 9055 2835 6948 3059 5234 ...
    ##  $ V6 : Factor w/ 5 levels "A61","A62","A63",..: 5 1 1 1 1 5 3 1 4 1 ...
    ##  $ V7 : Factor w/ 5 levels "A71","A72","A73",..: 5 3 4 4 3 3 5 3 4 1 ...
    ##  $ V8 : int  4 2 2 2 3 2 3 2 2 4 ...
    ##  $ V9 : Factor w/ 4 levels "A91","A92","A93",..: 3 2 3 3 3 3 3 3 1 4 ...
    ##  $ V10: Factor w/ 3 levels "A101","A102",..: 1 1 1 3 1 1 1 1 1 1 ...
    ##  $ V11: int  4 2 3 4 4 4 4 2 4 2 ...
    ##  $ V12: Factor w/ 4 levels "A121","A122",..: 1 1 1 2 4 4 2 3 1 3 ...
    ##  $ V13: int  67 22 49 45 53 35 53 35 61 28 ...
    ##  $ V14: Factor w/ 3 levels "A141","A142",..: 3 3 3 3 3 3 3 3 3 3 ...
    ##  $ V15: Factor w/ 3 levels "A151","A152",..: 2 2 2 3 3 3 2 1 2 2 ...
    ##  $ V16: int  2 1 1 1 2 1 1 1 1 2 ...
    ##  $ V17: Factor w/ 4 levels "A171","A172",..: 3 3 2 3 3 2 3 4 2 4 ...
    ##  $ V18: int  1 1 2 2 2 2 1 1 1 1 ...
    ##  $ V19: Factor w/ 2 levels "A191","A192": 2 1 1 1 1 2 1 2 1 1 ...
    ##  $ V20: Factor w/ 2 levels "A201","A202": 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ V21: int  1 2 1 1 2 1 1 1 1 2 ...
    

首先加载neuralnet包尝试一下，只用数值型变量建模，没有报错。

    library("neuralnet")
    NNModelAllNum <- neuralnet(V21 ~ V2 + V5 + V8, data)
    NNModelAllNum
    

    ## Call: neuralnet(formula = V21 ~ V2 + V5 + V8, data = data)
    ## 
    ## 1 repetition was calculated.
    ## 
    ##         Error Reached Threshold Steps
    ## 1 104.9993578    0.005128715177    55
    

当我们把V1放入解释变量中出现了如下错误：

    NNModel <- neuralnet(V21 ~ V1 + V2 + V5 + V8, data)
    

    ## Error: 需要数值/复数矩阵/矢量参数
    

此时可以用model.matrix函数将V1转化为三个虚拟变量，V1A12,V1A13,V1A14。

    dummyV1 <- model.matrix(~V1, data)
    head(cbind(dummyV1, data$V1))
    

    ##   (Intercept) V1A12 V1A13 V1A14  
    ## 1           1     0     0     0 1
    ## 2           1     1     0     0 2
    ## 3           1     0     0     1 4
    ## 4           1     0     0     0 1
    ## 5           1     0     0     0 1
    ## 6           1     0     0     1 4
    

因为model.matrix函数对数值型和分类Level=2的类别型变量没有影响，所以可以将四个变量一起用该函数生成新的数据集modelData，就可以用该数据集建模了。

    modelData <- model.matrix(~V1 + V2 + V5 + V8 + V21, data)
    head(modelData)
    

    ##   (Intercept) V1A12 V1A13 V1A14 V2   V5 V8 V21
    ## 1           1     0     0     0  6 1169  4   1
    ## 2           1     1     0     0 48 5951  2   2
    ## 3           1     0     0     1 12 2096  2   1
    ## 4           1     0     0     0 42 7882  2   1
    ## 5           1     0     0     0 24 4870  3   2
    ## 6           1     0     0     1 36 9055  2   1
    

    NNModel <- neuralnet(V21 ~ V1A12 + V1A13 + V1A14 + V2 + V5 + V8, modelData)
    

另外一种方法来自nnet package的class.ind函数。

    library("nnet")
    dummyV12 <- class.ind(data$V1)
    head(dummyV12)
    

可以看到，该结果和model.matrix稍有区别，生成了四个虚拟变量。要注意，为了避免多重共线性，对于level=n的分类变量只需选取其任意n-1个虚拟变量。

    ##      A11 A12 A13 A14
    ## [1,]   1   0   0   0
    ## [2,]   0   1   0   0
    ## [3,]   0   0   0   1
    ## [4,]   1   0   0   0
    ## [5,]   1   0   0   0
    ## [6,]   0   0   0   1