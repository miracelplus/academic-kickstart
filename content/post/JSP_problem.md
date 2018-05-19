+++
title = "车间调度问题(JSP)的解决方法"
date = 2018-05-19
draft = false
summary = "运筹学大作业使用车间调度问题，总结一下几种解决方法"

tags = ["Operations"]
categories = ["JSP","linear programming"]

[header]
image = ""
caption = ""
preview = true

+++

>[运筹学的大作业](/pdf/Project.pdf)要求解决一个车间调度问题(JSP)，这里列举一下几种解决方案

#### 车间调度问题(JSP)

这部分的介绍可以参看[车间作业调度](https://wenku.baidu.com/view/1fc939aef524ccbff12184dc.html)以及[车间调度问题综述](/pdf/作业车间调度问题综述.pdf)。

单车间调度问题（Job-shop scheduling problem, JSP）是最基本、最著名的调度问题，也是NP难问题，无最优解精确算法。

一般类型的JSP问题可表达为：n个工件在m台机器上加工，每个工件有特定的加工工艺，每个工件加工的顺序及每道工序所花时间给定，安排工件在每台机器上工件的加工顺序，使得某种指标最优。

题设为：
不同工件的工序之间无顺序约束；
工序开始则不能间断，每个机器在同一时刻只能加工一个工序；
机器不发生故障。
调度的目标就是确定每个机器上工序的加工顺序和每个工序的开工时间，使最大完工时间(makespan)最小或其他指标达到最优。JSP调度问题简明表示为$n/m/G/C_{max}$。
