---
title: 动态局部回归过程
author: c cm
layout: post
permalink: /loess-in-gif/
duoshuo_thread_id:
  - 1235579490248163348
post_views_count:
  - 0
categories:
  - 数据可视化
tags:
  - 回归问题
---
局部回归Loess作为简单线性回归和最近邻KNN思想结合的产物，不仅直观，而且比较灵活。在数据探索过程中，对二元散点图做局部回归是基本步骤。

在R中用loess()和lowess()函数即可简单实现局部回归。

[Simply Statistics][1]用图像解释了loess的过程。

![loess in gif][2]

 [1]: http://simplystatistics.org/2014/02/13/loess-explained-in-a-gif/loess/
 [2]: http://simplystatistics.org/wp-content/uploads/2014/02/loess.gif