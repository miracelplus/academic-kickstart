+++
title = "Docker使用教程"
date = 2018-06-07T13:17:26+08:00
draft = false
summary = "人工智能导论大作业准备使用docker完成，因此记录一下docker的使用方法"

tags = ["Docker"]
categories = ["Technique"]

[header]
image = ""
caption = ""
preview = true

+++

### Docker使用教程

>人工智能导论大作业准备使用docker完成，因此记录一下docker的使用方法

本教程基于windows10上的docker版本完成，之后的部分会更新在本页面。

#### dockerfile的使用

dockerfile事实上是一系列在docker上配置的命令指令的文本文档。
在pix2pix的实现界面上，作者提供了一个dockerfile来进行环境的配置，要使用该dockerfile，可以利用powershell到该文件夹下，运行命令 docker build -t Imagename .
注意Imagename为自己设定，后面的那个.千万别忘了。

#### 全局加速

由于不可名状的原因，国内连接docker hub的速度实在是令人发指，更多的时候是超时错误根本无法连接，因此选择国内的加速器镜像网站daocloud.io
##### Docker For Windows
在桌面右下角状态栏中右键 docker 图标，修改在 Docker Daemon 标签页中的 json ，把下面的地址:
http://65977335.m.daocloud.io
加到"registry-mirrors"的数组里。点击 Apply 。
这样就可以下载到docker镜像了