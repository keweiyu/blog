---
title: data.table包问题整理
author: c cm
layout: post
permalink: /data_table_package_QA/
categories: R语言
tags:
description: data.table包比tapply快10+倍，比==快100+倍, 比DF[i, j] <- value快500+倍，绝对是值得花精力去学习的一个包。

---
data.table包比tapply快10+倍，比==快100+倍, 比DF[i, j] <- value快500+倍，绝对是值得花精力去学习的一个包。

![值得花多少时间](http://imgs.xkcd.com/comics/is_it_worth_the_time.png)


对于熟悉SQL的同学，data.table使用方法可以简单地概括为：

`DT[where,select|update,group by][having][order by][ ]...[ ]`

## Q: 读入数据
```r
DT <- fread("filelocation")
DT2 <- data.table(read.table("filelocation"))
```

## Q: 新增/删除/更新变量
```r
# add
DT[, varnew := var1 + var2]
DT[, `:=`(varnew1 = var1 * var2, varnew2 = var1 / var2)]
DT[, c("varnew1", "varnew2") := list(tmp <- var1 * var2, tmp/100)] # 如果要接着用tmp, 此处只能用"<-"
# delete
DT[, var3 := NULL]
DT[, c("var4", "var5") := NULL]
# update
DT[, varupdate := ifelse(var1 > var2, 0, varupdate)]
DT[var1 > var2, varupdate := 0]
```

## Q：j中函数使用变量名 
```r
fun <-function(x,y,where=parent.frame()){
	return(get(x,where) * get(y,where))
	}
DT[, lapply(c('var1','var2'), fun, y ='var3', where=.SD)]
```

## Q: 错误提示result for group n has column n type type_x(eg:'integer') but expecting type type_y(eg:'double')

这是由于data.table包在计算完第一组变量后，会自动设定各变量的变量类型type_x。如果后面有一组的变量计算结果为type_y，就会导致此错误。比如计算变量mean(1:9), 结果是5，变量类型为integer，当后面某组计算mean(1:10)这样返回值5.5是double类型时，就会出现错误。

解决方法很简单，统一变量类型。比如用as.double(mean(vector))将返回值强制变量类型为double就可以了。