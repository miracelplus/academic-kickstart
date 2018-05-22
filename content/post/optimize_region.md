+++
title = "优化得到一个区域的思路"
date = 2018-05-08T14:19:26+08:00
draft = false
summary = "在进行最优化问题的时候(在本次项目中)，我们需要得到一个比较稳定的value region，利用最优化的方法目前只能得到一个个最优的点，不是太科学，探索一下具体应该怎么做。"

tags = ["optimize","value region"]
categories = ["Operation","Optimize"]

[header]
image = ""
caption = ""
preview = true

+++

#### 优化得到一个区域的思路

>在进行最优化问题的时候(在本次项目中)，我们需要得到一个比较稳定的value region，利用最优化的方法目前只能得到一个个最优的点，不是太科学，探索一下具体应该怎么做。

目前是启用一个最优化的算法，得到最优点之后向周围做一个搜索(不同的x,y)，如果所有的区域都能在我们要求的threhold里，那么就将这部分的点全部放进去。