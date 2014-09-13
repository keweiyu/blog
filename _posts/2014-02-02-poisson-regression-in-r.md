---
title: R语言中的泊松回归
author: c cm
layout: post
permalink: /poisson-regression-in-r/
duoshuo_thread_id:
  - 1235579490248163340
post_views_count:
  - 1
categories:
  - R语言
tags:
  - 回归问题
---
泊松回归用来对计数型反应变量或者列联表进行回归分析，是在广义线性模型中除逻辑回归外最常用的回归分析模型。泊松回归假设反应变量服从泊松分布，反应变量的期望值的对数可以用线性回归分析（即连接函数为对数函数），泊松回归也被称为对数线性回归。<!--more-->

# 一、泊松分布

泊松分布由二项分布衍伸而来，适合描述某种事件在一定期间内发生的次数。比如，病人在一周内发病次数，工厂一月内劣质产品生产个数，排队论中一段时间内到达顾客的数量等。

泊松分布的概率质量函数为  
<img src="//s0.wp.com/latex.php?latex=P%28X+%3D+k%29+%3D+%5Cfrac%7Be%5E%7B-%5Clambda+%7D%5Clambda+%5E%7Bk%7D%7D%7Bk%21%7D&#038;bg=ffffff&#038;fg=000&#038;s=0" alt="P(X = k) = &#92;frac{e^{-&#92;lambda }&#92;lambda ^{k}}{k!}" title="P(X = k) = &#92;frac{e^{-&#92;lambda }&#92;lambda ^{k}}{k!}" class="latex" />  
其中<img src="//s0.wp.com/latex.php?latex=%5Clambda+%3D+E%28X%29+%3D+Var%28X%29&#038;bg=ffffff&#038;fg=000&#038;s=0" alt="&#92;lambda = E(X) = Var(X)" title="&#92;lambda = E(X) = Var(X)" class="latex" />

在R中，用dpois()计算该概率。比如，如果一周内买胡萝卜的兔子平均数量为3（<img src="//s0.wp.com/latex.php?latex=%5Clambda+%3D+3&#038;bg=ffffff&#038;fg=000&#038;s=0" alt="&#92;lambda = 3" title="&#92;lambda = 3" class="latex" />），那么一周内买胡萝卜的兔子数量为k(k=0,1,2,3,&#8230;,10)的概率分别为：

    for (k in 0:10) {
        rabbit[k + 1] <- round(dpois(x = k, lambda = 3), 2)
    }
    rabbit
    

    ##  [1] 0.05 0.15 0.22 0.22 0.17 0.10 0.05 0.02 0.01 0.00 0.00
    

在上面的例子中，其实还隐含了几个假设。只有在这四个假设条件下，用来计数的随机变量才服从泊松分布。（参考<a href="http://www.pmean.com/definitions/poisson.htm" target="_blank">pmean</a>)

1.  一小段时间内事件发生的概率与该段时间的长短呈比例(proportional)
2.  在一个极小时间段内事件发生两次以上的概率可以忽略(negligible)
3.  无论时间先后，事件在一段给定时间内发生的概率稳定(stable)
4.  非重叠时间段内事件发生的概率相互独立(independent)

# 二、泊松回归

当反应变量服从泊松分布时，用OLS线性回归得到的结果不佳，因为

1.  记数变量>=0,而OLS不能保证这一点
2.  反应变量和自变量之间的关系非线性

R中用glm()函数进行泊松回归，￼￼具体形式为：  
`glm(Y ~ X1 + X2, family = poisson(link = "log"), data = dataframe)`

实际是以log()为连接函数的对数线性回归：  
<img src="//s0.wp.com/latex.php?latex=log%28Y%29+%3D+%5Calpha+%2B%5Cbeta+X&#038;bg=ffffff&#038;fg=000&#038;s=0" alt="log(Y) = &#92;alpha +&#92;beta X" title="log(Y) = &#92;alpha +&#92;beta X" class="latex" />

下面以Princeton University提供的数据集<a href="http://data.princeton.edu/wws509/datasets/#ceb" target="_blank">The Children Ever Born Data</a>为例。读者也可在以上网址找到其他根据统计分析类型（线性回归、逻辑回归、泊松回归、指数线性回归、生存分析）分类的其他数据集。

    ceb <- read.table("http://data.princeton.edu/wws509/datasets/ceb.dat")
    names(ceb)
    

    ## [1] "dur"  "res"  "educ" "mean" "var"  "n"    "y"
    

变量说明如下：  
The cell number (1 to 71, cell 68 has no observations),  
&#8220;dur&#8221; = marriage duration (1=0-4, 2=5-9, 3=10-14, 4=15-19, 5=20-24, 6=25-29),  
&#8220;res&#8221; = residence (1=Suva, 2=Urban, 3=Rural),  
&#8220;educ&#8221; = education (1=none, 2=lower primary, 3=upper primary, 4=secondary+),  
&#8220;mean&#8221; = mean number of children ever born (e.g. 0.50),  
&#8220;var&#8221; = variance of children ever born (e.g. 1.14), and  
&#8220;n&#8221; = number of women in the cell (e.g. 8),  
&#8220;y&#8221; = number of children ever born.

给响应变量育子数做直方图，可以清楚看到其偏倚度。

    hist(ceb$y, breaks = 50, xlab = "children ever born", main = "Distribution of CEB")
    

![plot of chunk unnamed-chunk-1][1]  
下面对其做泊松回归，并输出结果。

    
    fit <- glm(y ~ educ + res + dur, offset = log(n), family = poisson(), data = ceb)
    summary(fit)
    

    ## 
    ## Call:
    ## glm(formula = y ~ educ + res + dur, family = poisson(), data = ceb, 
    ##     offset = log(n))
    ## 
    ## Deviance Residuals: 
    ##    Min      1Q  Median      3Q     Max  
    ## -2.291  -0.665   0.076   0.661   3.679  
    ## 
    ## Coefficients:
    ##             Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)   0.0570     0.0480    1.19     0.24    
    ## educnone     -0.0231     0.0227   -1.02     0.31    
    ## educsec+     -0.3327     0.0539   -6.17  6.7e-10 ***
    ## educupper    -0.1247     0.0300   -4.16  3.2e-05 ***
    ## resSuva      -0.1512     0.0283   -5.34  9.4e-08 ***
    ## resurban     -0.0390     0.0246   -1.58     0.11    
    ## dur10-14      1.3705     0.0511   26.83  < 2e-16 ***
    ## dur15-19      1.6142     0.0512   31.52  < 2e-16 ***
    ## dur20-24      1.7855     0.0512   34.86  < 2e-16 ***
    ## dur25-29      1.9768     0.0500   39.50  < 2e-16 ***
    ## dur5-9        0.9977     0.0528   18.91  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for poisson family taken to be 1)
    ## 
    ##     Null deviance: 3731.525  on 69  degrees of freedom
    ## Residual deviance:   70.653  on 59  degrees of freedom
    ## AIC: Inf
    ## 
    ## Number of Fisher Scoring iterations: 4
    

为了更好地解释模型参数，将其指数化。

    exp(coef(fit))
    

    ## (Intercept)    educnone    educsec+   educupper     resSuva    resurban 
    ##      1.0586      0.9772      0.7170      0.8827      0.8597      0.9618 
    ##    dur10-14    dur15-19    dur20-24    dur25-29      dur5-9 
    ##      3.9374      5.0240      5.9625      7.2196      2.7119
    

可见随着婚龄的增长，期望的育子数将相应增长；教育程度越高，期望育子数越低；农村预期育子数比城市高等。

泊松回归中需要注意过度离势问题。泊松分布中均值与方差相等，当观测到的响应变量实际分布不满足这一点时，泊松回归可能会出现这样的问题。这个问题一般原因是缺少解释变量。我们可以用qcc包对泊松模型检验过度离势。

    require(qcc)
    qcc.overdispersion.test(ceb$y, type = "poisson")
    

    ##                    
    ## Overdispersion test Obs.Var/Theor.Var Statistic p-value
    ##        poisson data               323     22284       0

p值为0，果然该数据存在过度离势的情况，可以用类泊松模型对数据进行分析。

    fit2 <- glm(y ~ educ + res + dur, offset = log(n), family = quasipoisson(), data = ceb)
    summary(fit2)
    

    ## 
    ## Call:
    ## glm(formula = y ~ educ + res + dur, family = quasipoisson(), 
    ##     data = ceb, offset = log(n))
    ## 
    ## Deviance Residuals: 
    ##    Min      1Q  Median      3Q     Max  
    ## -2.291  -0.665   0.076   0.661   3.679  
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   0.0570     0.0529    1.08  0.28596    
    ## educnone     -0.0231     0.0249   -0.93  0.35854    
    ## educsec+     -0.3327     0.0593   -5.61  5.7e-07 ***
    ## educupper    -0.1247     0.0330   -3.78  0.00037 ***
    ## resSuva      -0.1512     0.0312   -4.85  9.4e-06 ***
    ## resurban     -0.0390     0.0271   -1.44  0.15597    
    ## dur10-14      1.3705     0.0562   24.37  < 2e-16 ***
    ## dur15-19      1.6142     0.0564   28.64  < 2e-16 ***
    ## dur20-24      1.7855     0.0564   31.66  < 2e-16 ***
    ## dur25-29      1.9768     0.0551   35.88  < 2e-16 ***
    ## dur5-9        0.9977     0.0581   17.18  < 2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for quasipoisson family taken to be 1.212)
    ## 
    ##     Null deviance: 3731.525  on 69  degrees of freedom
    ## Residual deviance:   70.653  on 59  degrees of freedom
    ## AIC: NA
    ## 
    ## Number of Fisher Scoring iterations: 4
    

比较以上两个模型参数结果，发现参数估计值一致，而t值/p值不同。在过度离势的情况下，应采用类泊松结果的t值/p值检验自变量的显著程度。

 [1]: data:image/png;base64,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f/4kf9YblCrwt0rGC/wP8rhaXdFXq4LY1prjjrvV982r3XLNotOfX0+v2yR1B+DUsWfcNetHJR9OPja2/eKs/5+xx9NW/zU76k5F0+o4VfTrmAp8o2LZe4Fp/wB7P3ADJLCRBfbN6z8iqavIj7eMZZtsqyvcjyRVtOp71nUb/abJ4Um1usqvNvlGuymQa/ZO//PAsefed9TtnNGy3iRUqyvwajdLmsK6ekP+mPb6zf6661Dt/snWq9fW/ABdFfcbkq+NttWirtxrW5fWHPdhY50fOlo/c2zbulY/OOpQhXeH0Xp9TXtZ8pSk7jQsotWbiGptPwewZo8/CfRQYPILSw+HaEgEeiPwroykrmDrtm1dJda/n7oiPCuZbFXw/i7ZK3lR8vWknldt1eo/f34b+rfzuK60m9vgTeEddZu62D17zkjqDcbTR73eOlo2Y/qLPK43G4clW432NYsrRyvN67+q2TFarsry08mDkuZ1Hpf1ascll61em/+PeqPz50kdq0wuGa3Xeb806drelI5PTu6R1JuNU5ODkl2TmqdjkvFWb0zq6nyy1RuFOlbTfj8rD0w2S+r2/z2TajX/GgECBAgsI4G6Gq6C3eRHWf/P5GnJ+F2w40d9HptltV9NTkjq+9313Co6VRib59wl6z9Mat85ybaj9R9kOd4OzYPq86HRxsNHj6sgnzRar6v+lyVVlKrVlfspST3vmuTY5FOjx/XGoNrk69e26lvPacZYV+7vSKqY1/YfJy9Pxt8s1GuPvynZO4+r75nJtHbn7DgtqTsg1XdV8vCkaUdkpba/vtkwZVkF+A3J5Un1b163CnTT6u5As69t+ZpRx7dM9Ks7Etcm5yavTLZINAIECBAg8P8FtszayqSWk60K5R5JU1An93d5fOt0am6hT/avfTed3Dj2uOvr1/jqjUHdaVhk2yYH23kBB6w3Nnsmza36BRzSIQgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIECAAAECBAgQIDCfwGbzdd+kez87Z7/THALbpO/xyZfneI6uBAgQIEBgIQJbLOQom8ZBHpDTfNkcp7pd+j4mUeDnQNOVAAECBBYjoMB3d7w2XU/r3n3FQel7wxz9dSVAgAABAgsTuMnCjuRABAgQIECAQG8EFPjeTIWBECBAgACBxQko8IuzdCQCBAgQINAbAQW+N1NhIAQIECBAYHECCvziLB2JAAECBAj0RkCB781UGAgBAgQIEFicgAK/OEtHIkCAAAECvRFQ4HszFQZCgAABAgQWJ6DAL87SkQgQIECAQG8EFPjeTIWBECBAgACBxQko8IuzdCQCBAgQINAbAQW+N1NhIAQIECBAYHECCvziLB2JAAECBAj0RkCB781UGAgBAgQIEFicgAK/OEtHIkCAAAECvRFQ4HszFQZCgAABAgQWJ6DAL87SkQgQIECAQG8EFPjeTIWBECBAgACBxQko8IuzdCQCBAgQINAbAQW+N1NhIAQIECBAYHECCvziLB2JAAECBAj0RkCB781UGAgBAgQIEFicgAK/OEtHIkCAAAECvRFQ4HszFQZCgAABAgQWJ6DAL87SkQgQIECAQG8EFPjeTIWBECBAgACBxQko8IuzdCQCBAgQINAbAQW+N1NhIAQIECBAYHECfS7wO+U0t1jcqToSAQIECBDYdAT6UuCPD/k+I/Y7ZvnPyfnJxckbki0TjQABAgQIEOgo0JcCv2/Gu81ozM/L8uzktsnBycqktmkECBAgQIBAR4G+FPjx4T44D45JLk2+kbwgOSTRCBAgQIAAgY4CfSrwdbV+m+QLyS3Hxr9f1k8fe2yVAAECBAgQWIdAX36I7V0Z5yOSFybbJ9cmj0+OSY5MHpBoBAgQIECAQEeBvhT412S8lWq7JtutXlux4oQsX51cNXq8rsVT0+ExUzrtkO2fTF48Zb/NBAgQIEBg2Qj0pcCPg16QB5Vqdbt+8+SmyU+SdbX/mw7vmdLp0dleRV4jQIAAAQLLXqAv34PfPdLHJ3Wl/ulkr6Rph2WlCneXdn061e39tlyX7Td2OYg+BAgQIEBg6AJ9KfBHBfKi5IDktOTkZO9EI0CAAAECBJYg0Jdb9A/L2PdPrklelHwtqe+X3zvRCBAgQIAAgTkF+nIFXwW9rt6b9t6svD75RDL+K3PNfksCBAgQIEBghkBfCvybMsYPJEePjfXYrH8oee3YNqsECBAgQIBAB4G+3KL/VMZ6h+T2E2N+SR6fNNo3sctDAgQIECBAYJpAXwp8je/q5MyWgX4u2yoaAQIECBAg0FGgL7foOw5XNwIECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLgALfRUkfAgQIECAwMAEFfmATZrgECBAgQKCLQB8L/BYZ+A5dBq8PAQIECBAg0C7QlwK/VYb3iuT85KfJpcnVyVeSpyYaAQIECBAgMIdAXS33ob0+g9gl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