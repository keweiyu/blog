---
title: Hadoop生态圈简介
author: c cm
layout: post
permalink: /hadoop-ecosystem/
duoshuo_thread_id:
  - 1235579490248163353
categories:
  - 大数据
tags:
  - hadoop
---
除了Core Hadoop（通常包括HDFS和MapReduce）之外，Hadoop生态圈现在还包括了许多软件使得Hadoop更易于使用。<!--more-->

## Core Hadoop

### HDFS

HDFS是Hadoop的文件系统或者存储机制。特点是重复性、可容错、可扩展，旨在处理大型数据。

### MapReduce

MapReduce是一种分布式数据处理模型，将一项任务拆分为多个小任务并同时加以实施。

## Hadoop Ecosystem

### Hive

可以将Hive理解为包装成标准SQL的MapReduce。在Hive中，可以用标准SQL查询语句（类似在MySQL中的 SELECT * FROM &#8230; WHERE&#8230;)处理数据，Hive将标准SQL翻译为MapReduce。

### Pig

Pig是一种较为简单的数据流语言，主要用于大规模数据集的分析。Pig和Hive一样，都会翻译成MapReduce作业，所以耗时较多。

### Impala

Impala和前二者不同，不翻译成MapReduce，而是以不同的思路直接连接HDFS。Impala和Hive一样可以转换标准SQL语句，而且运行非常快。

### Sqoop

Sqoop将传统关系数据库数据导入/导出HDFS。

### Flume

Flume将日志类数据导入HDFS。

### HBase

Hbase是利用HDFS作为底层存储的高度可扩展性数据库，支持MapReduce和随机访问。

### Hue

Hue允许用户通过WEB程序以图形化的界面与Hadoop生态圈进行交互。

### Oozie

Oozie是工作流管理工具，允许用户利用Hive、Pig以及MapReduce等其它项目创建复杂工作流。内置逻辑允许用户按预定步骤处理故障。

### Mahout

Mahout是机器学习库，可以在MapReduce中运行机器学习算法。

# Cloudera将上述软件整合在CDH中。

# 参考：《Hadoop权威指南》