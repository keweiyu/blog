---
title: 排名算法简介
author: c cm
layout: post
permalink: /ranking_algorithms_introduction/
categories: 机器学习
tags:
description: 
---
想要知道一个player的水平如何，最简单的方式就是看他的排名。比如[FIFA World Rankings](http://www.fifa.com/worldranking/rankingtable/index.html)，[WTA Rankings](http://www.wtatennis.com/singles-rankings)等等。这些竞赛排名通常会与胜负率、比赛的重要程度、参与比赛的场数有直接关系，往往不能反应player的“真实”水平。

## ELO
ELO最初是象棋使用，社交网络中“we are rating girls"后也曾出现过ELO的公式。

![社交网络elo公式](http://www.leighh.com/wp-content/uploads/2010/11/1286316260-The-Social-Network-window-algorithm.gif)

何解？假设妹子A和妹子B的貌美如花程度分别为Ra(Rating a)和Rb，初始情况下设Ra = Rb = 1500。在经过一轮打分后，将咳咳妹子分数的一部分拿出来奖励给漂亮妹子。分数的update公式为：

$R' = R + K * (S - E)$

$R'$ = 更新后分数

$R$ = 更新前分数

$K$ = 分数变动的最大值

$S$ = 实际打分结果(1 if win, 0 if lose, 0.5 if draw)

$E$ = 期望打分结果（就是图片中的公式啦）

$Ea = 1/(1 + 10^{(Rb - Ra)/400} )$

$Eb = 1/(1 + 10^{(Ra - Rb)/400} )$

###举个栗子

$Ra = 1900，Rb = 1500，K = 32$

那么 $Ea = 1/(1 + 10^{(1500 - 1900)/400} ) = 0.91$,
$Eb = 1/(1 + 10^{(1900 - 1500)/400} ) = 0.09$。
计算容易得到，400分的差值代表91％的胜率，200分的差值代表76％的胜率。

如果A赢，则
$Ra' = Ra + K * (S - E) = 1900 + 32 * (1 - 0.91) = 1902.88$
$Rb' = Rb + K * (S - E) = 1600 + 32 * (0 - 0.09) = 1597.12$

如果B赢，则
$Ra' = Ra + K * (S - E) = 1900 + 32 * (0 - 0.91) = 1870.88$
$Rb' = Rb + K * (S - E) = 1600 + 32 * (1 - 0.09) = 1629.12$

可以看到，$Ra' + Rb' = Ra + Rb$，在A赢得91％概率的情况下，B输的代价并不大，而A输的代价则大得多。这些都是由公式根据AB两者分数差值大小决定的。

ELO中的最大主观因素在于K值的选取。在象棋中，一般认为master表现水平稳定，K值应该较小（16），而新手表现波动较大，K值应该更大（32）。当然，如果对不同人取不同K的话，$Ra' + Rb' = Ra + Rb$的关系就不一定成立了。

## Glicko
Glicko对ELO做出的最大改进是引入了分数的波动，即Rating Deviation（RD）。

###STEP1 计算RD
如果第一次play，初始化为$R = 1500, RD = 350$

否则 $RD = min(\sqrt{RD^2 + c^2t}, 350)$

其中
t = 玩家从上次比赛到现在间隔的时间
c = 用来控制玩家uncertainty的一个常数，
距离上次比赛的时间越长，选手的变动越大，最小不小于350。

###STEP2 计算$R'$

$RD' = \sqrt{(1/RD^2 + 1/d^2)^{-1}}$
$$R' = R +q*RD^2*g({RD}_{oppo})*(s - E(s\|r, {r}_{oppo}, {RD}_{oppo}))$$

其中
$q = ln(10)/400$

$g(RD) = 1/(\sqrt{1 + 3q^2RD^2/\pi^2})$是一个随RD递减的函数

$$E(s\|r, {r}_{i}, {RD}_{oppo}) = 1/(1+10^{-g({RD}_{oppo})(r-{r}_{oppo})/400})$$，当对手${RD}_{oppo} = 0$时，与ELO的Ei等同

$$1/d^2 = q^2 g({RD}_{oppo})^2  E(s\|r, {r}_{oppo}, {RD}_{oppo})(1- E(s\|r, {r}_{oppo}, {RD}_{oppo}))$$，$${RD}_{oppo}$$越小，$$g({RD}_{oppo})$$越大，$$E(s\|r, {r}_{oppo}, {RD}_{oppo})$$越不确定（越靠近0.5），$1/d^2$越大，$RD'$越小。

###举个栗子
$Ra = 1900，Rb = 1500，K = 32$
首先假设$RDa = RDb = 350$。
$$g({RD}_{b}) =  g({RD}_{a}) = 1/(\sqrt{1 + 3q^*350^2/\pi^2}) = 0.6690694$$
$$E({s}_{a}\|r, {r}_{b}, {RD}_{b}) =  1/(1+10^{-0.6690694*(1900-1500)/400}) = 0.8235504$$
$$E({s}_{b}\|r, {r}_{a}, {RD}_{a}) =  1/(1+10^{-0.6690694*(1500-1900)/400}) = 0.1764496$$
$$1/{d}_{a}^2 = 1/{d}_{b}^2 =  q^2* 0.6690694^2*0.8235504*(1- 0.8235504)=2.155582e-06$$

$${RD}_{a}' = {RD}_{b}' = 1/\sqrt{(1/350^2 + 2.155582e-06)} = 311.3038$$

如果A赢，那么
$$Ra' = 1900 + q*350^2*0.6690694*(1 - 0.8235504) = 1983.25$$
$$Rb' = 1500 + q*350^2*0.6690694*(0 - 0.1764496) = 1416.75$$

如果B赢，那么
$$Ra' = 1900 + q*350^2*0.6690694*(0 - 0.8235504) = 1511.444$$
$$Rb' = 1500 + q*350^2*0.6690694*(1 - 0.1764496) = 1888.556$$

## Stephenson
挖坑

## TrueSkill
挖坑