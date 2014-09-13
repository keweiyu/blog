---
title: 三门问题及解法
author: c cm
layout: post
permalink: /monty_hall_problem/
categories:
tags:
description: "第一次听到三门问题是在电影《决胜21点》中，教授给出游戏*Let's Make a Deal*的设定：

1. 向参赛者展示三道关闭的门，其中有一道门后有奖品一辆汽车，而其他两门后则两头山羊。门后汽车的摆放是随机的。如果参赛者猜中哪道门后有汽车，则赢得奖品。
2. 游戏过程是先由参赛者选择三门之一，我们假设是门x；然后，知道奖品在哪的主持人会在剩下的门y, 门z中选择一个门后没有奖品的打开。之后，主持人会询问参赛者是否坚持选择门x，或者换到剩下的另一种选择。

教授的问题是，此时参赛者应该坚持原有选择，还是换门？换不换会有区别嘛？
"
---


第一次听到[三门问题](http://en.wikipedia.org/wiki/Monty_Hall_problem)是在电影《决胜21点》中，教授给出游戏*Let's Make a Deal*的设定：

1. 向参赛者展示三道关闭的门，其中有一道门后有奖品一辆汽车，而其他两门后则两头山羊。门后汽车的摆放是随机的。如果参赛者猜中哪道门后有汽车，则赢得奖品。
2. 游戏过程是先由参赛者选择三门之一，我们假设是门x；然后，知道奖品在哪的主持人会在剩下的门y, 门z中选择一个门后没有奖品的打开。之后，主持人会询问参赛者是否坚持选择门x，或者换到剩下的另一种选择。

教授的问题是，此时参赛者应该坚持原有选择，还是换门？换不换会有区别嘛？

同学们纷纷表示没区别，奖品在剩下两门中任一一门的概率均为1/2。此时男主鄙视地说出了正确答案：换门获胜的概率为2/3。

## 解法1 简单列举
门x  | 门y   | 门z |坚持门x|换门
-----|------|-----|------|----
汽车 | 山羊  | 山羊 |中奖  |失败
山羊 | 汽车  | 山羊 |失败  |中奖
山羊 | 山羊  | 汽车 |失败  |中奖

by [vos Savant, Marilyn](http://marilynvossavant.com/game-show-problem/)

## 解法2 条件概率
假设参数者还是选择的x，事件
A = 猜中获奖
B = 主持人在之后打开了门y

 .     | p(A) | p(B\|A) |p(A)*p(B\|A)|p(A\|B)
-------|------|---------|------------|----
车在门x | 1/3  | 1/2     |1/6         |1/3
车在门y | 1/3  | 0       |0           |0
车在门z | 1/3  | 1       |1/3         |2/3

p(A)为参赛者刚开始选择时中奖的先验概率，为1/3。
如果车在y后，主持人不可能开门y, p(B|A) = 0;
如果车在x后，主持人会在门y,门z中任选一门，那么打开门y的概率为1/2；
如果车在门z后，主持人只能在y,z中开门y, 那么p(B|A) = 1。
接下来用贝叶斯公式就能得到结果：
P(A|B) = P(AB)/P(B) = P(A) * P(B|A) / (第三列之和 = 1/2)

## 解法3 模拟
![monty hall simu]({{ site.url }}/img/monty_hall_simu.png)

```r
require(ggplot2)
num_simu <- 1:5000
set.seed(12); car_pos <- sample(c('x', 'y', 'z'), length(num_simu), replace = T)
set.seed(95); player_guess <- sample(c('x', 'y', 'z'), length(num_simu), replace = T)
result_stay <- car_pos == player_guess
monty_choose <- function(car, player){
  sample(c('x', 'y', 'z')[!(c('x', 'y', 'z') %in% c(car, player))], 1)
}
monty_door <- mapply(monty_choose, car_pos, player_guess)
result_change <- car_pos != player_guess & car_pos != monty_door
result_stay_cum <- cumsum(result_stay)/num_simu
result_change_cum <- cumsum(result_change)/num_simu
monty_data <- data.frame(car_pos, player_guess, result_stay, monty_door, result_change, result_stay_cum, result_change_cum)
ggplot(monty_data, aes(num_simu)) +
  geom_line(aes(y = result_stay_cum, col = "result_stay_cum")) + 
  geom_line(aes(y = result_change_cum, col = "result_change_cum")) +
  ylab("Probability of Winning")
```