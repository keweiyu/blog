---
title: R语言中的正则表达式
author: c cm
layout: post
permalink: /regular-expression-r-language/
duoshuo_thread_id:
  - 1235579490248163342
post_views_count:
  - 0
categories:
  - R语言
  - 大数据
tags:
  - 正则表达式
---
能使用正则表达式处理文本是R语言强大的特点之一。对正则表达式不熟悉的同学请出门左转恶补一下<a href="http://cdn.dzone.com/sites/all/files/refcardz/rc196-010d-regex_1.pdf" target="_blank">正则表达式语法速成教程（英文）</a>或者<a href="http://deerchao.net/tutorials/regex/regex.htm" target="_blank">正则表达式30分钟入门教程（中文）</a>。R语言的base包中正则表达式函数（<a href="http://stat.ethz.ch/R-manual/R-patched/library/base/html/grep.html" target="_blank">官方文档</a>）主要是以下几个：grep(), grepl(),sub(), gsub(), regexpr(), regexec(), gregexpr()。下面将分开介绍。<!--more-->

# 一 R语言中用到的正则表达式函数

*   grep,grepl函数搜索字符串中是否符合正则表达式，返回行标或逻辑向量
*   regexpr,gregexpr,regexec返回正则表达式匹配开始的字符位置，及匹配的字符个数
*   sub,gsub用另一个字符串替换匹配的字符串

# 二 用正则表达式抓取网页数据实例

正则表达式函数在网页数据抓取中必备。此次通过一个网页抓举实例说明上面几个函数的作用。比如我们现在希望提取dribble网站某页面的配色方案，作为以后colorRampPalette()函数的参数。

## 1 准备工作

[<br class="Apple-interchange-newline" /><img class="size-full wp-image-159 alignleft" alt="dribble-palette" src="http://iccm.cc/wp-content/uploads/2014/01/dribble-palette.png" width="252" height="31" />][1]

首先进入dribble任意一页，如<a href="http://dribbble.com/shots/1124784-CCM-icon" target="_blank">CCM icon</a>。可以看见网页右侧提供了该图像的配色方案。然后用开发者工具查看网页源代码，找到与上面图片对应的如下片段。其中以#字符开头的6位字符就是我们的目标。

    <ul class="color-chips group">
     <li class="color">
     <a href="/colors/201F1E" style="background-color: #201F1E" title="#201F1E">#201F1E</a>
     </li>
     <li class="color">
     <a href="/colors/D9D8CF" style="background-color: #D9D8CF" title="#D9D8CF">#D9D8CF</a>
     </li>
     <li class="color">
     <a href="/colors/60605C" style="background-color: #60605C" title="#60605C">#60605C</a>
     </li>
     <li class="color">
     <a href="/colors/A9A9A1" style="background-color: #A9A9A1" title="#A9A9A1">#A9A9A1</a>
     </li>
     <li class="color">
     <a href="/colors/C5C5BB" style="background-color: #C5C5BB" title="#C5C5BB">#C5C5BB</a>
     </li>
     <li class="color">
     <a href="/colors/BFBFB5" style="background-color: #BFBFB5" title="#BFBFB5">#BFBFB5</a>
     </li>
     <li class="color">
     <a href="/colors/56452E" style="background-color: #56452E" title="#56452E">#56452E</a>
     </li>
     <li class="color">
     <a href="/colors/897466" style="background-color: #897466" title="#897466">#897466</a>
     </li>
    </ul>
    

## 2 R中读取网页并截取出上面片段

首先用readLines函数将整个页面的HTML代码下载到本地，存储至变量raw中。raw是长750行的字符向量。用grep函数，以&#8221;color-chips group&#8221;内容的ul开始与结束为条件，找出目标代码在raw中的位置，并提取出来。

<div>
  <pre><code>raw &lt;- readLines("http://dribbble.com/shots/1124784-CCM-icon")
grep("&lt;ul class=\"color-chips group\"&gt;", raw)
</code></pre>
  
  <pre><code>## [1] 285
</code></pre>
  
  <pre><code>grep("&lt;/ul&gt;", raw)
</code></pre>
  
  <pre><code>## [1]  74  95 122 136 310 655 667 678
</code></pre>
  
  <pre><code>raw[285:310]
</code></pre>
  
  <pre><code>##  [1] "&lt;ul class=\"color-chips group\"&gt;"                                         ##  [2] "      &lt;li class=\"color\"&gt;"                                               ##  [3] "        &lt;a href=\"/colors/201F1E\" style=\"background-color: #201F1E\" title=\"#201F1E\"&gt;#201F1E&lt;/a&gt;"
##  [4] "      &lt;/li&gt;"                                                              ##省略                                                                         
## [23] "      &lt;li class=\"color\"&gt;"                                              
## [24] "        &lt;a href=\"/colors/897466\" style=\"background-color: #897466\" title=\"#897466\"&gt;#897466&lt;/a&gt;"
## [25] "      &lt;/li&gt;"
## [26] "&lt;/ul&gt;"
</code></pre>
  
  <p>
    结果有26行，但是通过计算得到其中包含颜色信息的只有8行。
  </p>
  
  <pre><code>sum(grepl("background-color", raw))
</code></pre>
  
  <pre><code>## [1] 8
</code></pre>
  
  <p>
    通过grep函数，可以将这八行提取出来。<br /> 第一行利用grep函数返回的符合正则表达式的行标，对raw变量进行条件筛选，返回八行。<br /> 第二行通过将value参数设置为真，直接返回复合正则表达式的相应内容。
  </p>
  
  <pre><code>extract &lt;- raw[grep("background-color: ", raw)]
extract &lt;- grep("background-color: ", raw, value = TRUE)
extract[1:2]
</code></pre>
  
  <pre><code>## [1] "        &lt;a href=\"/colors/201F1E\" style=\"background-color: #201F1E\" title=\"#201F1E\"&gt;#201F1E&lt;/a&gt;"
## [2] "        &lt;a href=\"/colors/D9D8CF\" style=\"background-color: #D9D8CF\" title=\"#D9D8CF\"&gt;#D9D8CF&lt;/a&gt;"
</code></pre>
  
  <p>
    如果只利用grep()函数，不能直接得到我们想要的8个颜色代码。因为grep只能返回一个元素是否符合正则表达式，而不能指出是哪一部分符合正则表达式。下面介绍用regexpr()系列函数就可以和substr(),regmatches()配合；以及用sub系列函数反向操作，达到提取字符的目的。<br /> 下面列出以下几种方法：<br /> 1.regexpr() + substr()
  </p>
  
  <p>
    用regexpr()系列函数返回符合正则表达式的开始字符位置，及符合的字符数个数。其中，regexpr()和regexprc()分别以一个整数向量和一个整数列表形式返回第一个符合条件的开始位置；gregexpr()返回的是所有符合正则条件的开始位置。
  </p>
  
  <pre><code>regexpr("#[0-9A-Z]{6}", extract)
</code></pre>
  
  <pre><code>## [1] 59 59 59 59 59 59 59 59
## attr(,"match.length")
## [1] 7 7 7 7 7 7 7 7
## attr(,"useBytes")
## [1] TRUE
</code></pre>
  
  <pre><code>regexec("#[0-9A-Z]{6}", extract)[[1]]
</code></pre>
  
  <pre><code>## [[1]]
## [1] 59
## attr(,"match.length")
## [1] 7
</code></pre>
  
  <pre><code>gregexpr("#[0-9A-Z]{6}", extract)[[1]]
</code></pre>
  
  <pre><code>## [[1]]
## [1] 59 75 84
## attr(,"match.length")
## [1] 7 7 7
## attr(,"useBytes")
## [1] TRUE
</code></pre>
  
  <p>
    从以上三个结果都可以得到，符合条件的字符开始于第59位，总共7位。在substr()函数中使用这个信息，便可得出结果。
  </p>
  
  <pre><code>substr(extract, 59, 59 + 6)
</code></pre>
  
  <pre><code>## [1] "#201F1E" "#D9D8CF" "#60605C" "#A9A9A1" "#C5C5BB" "#BFBFB5" "#56452E"
## [8] "#897466"
</code></pre>
  
  <p>
    2. regexpr() + regmatches()
  </p>
  
  <pre><code>rule &lt;- regexpr("#[0-9A-Z]{6}", extract)
regmatches(extract, rule)
</code></pre>
  
  <pre><code>## [1] "#201F1E" "#D9D8CF" "#60605C" "#A9A9A1" "#C5C5BB" "#BFBFB5" "#56452E"
## [8] "#897466"
</code></pre>
  
  <p>
    3.sub()
  </p>
  
  <p>
    是利用sub()系列，反向思维，将符合正则表达式的部分用&#8221;&#8221;代替得到结果。要注意此时不能用在匹配到第一个正则表达式并替代后就停止的sub(),而应该使用全部字符匹配替代的gsub()。
  </p>
  
  <pre><code>sub("(.*)background-color: |\".*", "", extract)[1:2]
</code></pre>
  
  <pre><code>## [1] "#201F1E\" title=\"#201F1E\"&gt;#201F1E&lt;/a&gt;"
## [2] "#D9D8CF\" title=\"#D9D8CF\"&gt;#D9D8CF&lt;/a&gt;"
</code></pre>
  
  <pre><code>gsub("(.*)background-color: |\".*", "", extract)
</code></pre>
  
  <pre><code>## [1] "#201F1E" "#D9D8CF" "#60605C" "#A9A9A1" "#C5C5BB" "#BFBFB5" "#56452E"
## [8] "#897466"</code></pre>
</div>

 [1]: http://iccm.cc/wp-content/uploads/2014/01/dribble-palette.png